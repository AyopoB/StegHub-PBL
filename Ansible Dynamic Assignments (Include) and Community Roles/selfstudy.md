# **Self-Study Notes: Ansible Dynamic Assignments and Community Roles**

## **Overview**
This note explains how to use **dynamic assignments** with Ansible and leverage **community roles** from Ansible Galaxy to automate configurations efficiently. It also introduces conditional role execution for environment-specific configurations, load balancer selection, and integrating MySQL setup.



1. **Dynamic Assignments**:
   - Understand the difference between static (`import`) and dynamic (`include`) assignments.
   - Use dynamic assignments for real-time variable inclusion.

2. **Environment-Specific Configuration**:
   - Organize variables and playbooks for better management of multiple environments (Dev, Stage, UAT, Prod).

3. **Community Roles**:
   - Integrate community roles (e.g., `geerlingguy.mysql`) to streamline repetitive tasks like database setup.

4. **Conditional Role Execution**:
   - Use conditional logic to dynamically enable/disable roles based on environment-specific variables (e.g., switching between **Nginx** and **Apache** as load balancers).

---

## **1. Understanding Static vs. Dynamic Assignments**

| **Static Assignments**            | **Dynamic Assignments**        |
|-----------------------------------|--------------------------------|
| Use the `import` module.          | Use the `include` module.      |
| Processes tasks at the time of playbook parsing. | Processes tasks during execution. |
| Faster as tasks are preloaded.    | More flexible for real-time changes. |
| Best for predictable workflows.  | Useful for environment-specific configurations. |

> **Example**:  
**Static (`import_playbook`)**  
```yaml
- import_playbook: uat-webservers.yml
```

**Dynamic (`include_tasks`)**  
```yaml
- include_tasks: dynamic-assignments/env-vars.yml
```

---

## **2. Setting Up Dynamic Assignments**

Dynamic assignments are used to load variables or tasks at runtime, allowing flexibility in handling environment-specific configurations.

### **Steps for Setting Up Dynamic Assignments**

#### **Step 1: Organize Files and Directories**
1. Create a `dynamic-assignments` folder for storing dynamic configurations.
2. Add an `env-vars.yml` file for loading variables dynamically.
3. Create an `env-vars` folder to store environment-specific files (`dev.yml`, `uat.yml`, etc.).
4. Update the `site.yml` playbook to include dynamic variables.

**File Structure**:
```plaintext
├── dynamic-assignments
│   └── env-vars.yml
├── env-vars
│   ├── dev.yml
│   ├── stage.yml
│   ├── uat.yml
│   └── prod.yml
├── static-assignments
│   ├── common.yml
│   └── webservers.yml
└── site.yml
```

#### **Step 2: Configure `env-vars.yml`**
The `env-vars.yml` file dynamically loads the appropriate environment variables during playbook execution:
```yaml
- name: Collate variables from environment-specific file
  include_vars:
    file: "{{ item }}"
  with_first_found:
    - files:
        - "{{ inventory_file | basename | splitext | first }}.yml"
        - dev.yml
        - stage.yml
        - uat.yml
        - prod.yml
      paths:
        - "{{ playbook_dir }}/../env-vars"
```

**Key Points**:
- `include_vars`: Dynamically includes variables from environment files.
- `with_first_found`: Loads the first matching file in the specified order (e.g., `dev.yml` if `uat.yml` is not found).
- Special variables:
  - `{{ playbook_dir }}`: Refers to the directory of the playbook being executed.
  - `{{ inventory_file }}`: Dynamically identifies the inventory file in use.

#### **Step 3: Update `site.yml`**
Integrate the dynamic variable loader into your main playbook:
```yaml
- name: Include dynamic variables
  hosts: all
  become: yes
  tasks:
    - include_tasks: ../dynamic-assignments/env-vars.yml
```

---

## **3. Leveraging Community Roles**

Community roles, such as `geerlingguy.mysql`, provide pre-built solutions for common configurations. They save time and ensure best practices.

### **Installing a Community Role**
1. Install the role from Ansible Galaxy:
   ```bash
   ansible-galaxy install geerlingguy.mysql
   ```
2. Rename the role directory for clarity:
   ```bash
   mv geerlingguy.mysql mysql
   ```

### **Configuring the Role**
Edit the role variables in `roles/mysql/vars/main.yml`:
```yaml
mysql_root_password: "securepassword"

mysql_databases:
  - name: "my_database"
    encoding: utf8
    collation: utf8_general_ci

mysql_users:
  - name: "my_user"
    host: "192.168.1.%"
    password: "mypassword"
    priv: "my_database.*:ALL"
```

### **Using the Role in a Playbook**
Create a playbook (`db-servers.yml`) to configure MySQL using the role:
```yaml
- hosts: db
  become: yes
  roles:
    - role: mysql
```

Integrate this playbook into `site.yml`:
```yaml
- import_playbook: ../static-assignments/db-servers.yml
```

---

## **4. Configuring Load Balancer Roles**

For environments requiring load balancers, roles can dynamically configure **Nginx** or **Apache**.

### **Steps to Configure Load Balancers**

#### **Step 1: Install Roles**
Install and rename roles:
```bash
ansible-galaxy install geerlingguy.nginx
mv geerlingguy.nginx nginx

ansible-galaxy install geerlingguy.apache
mv geerlingguy.apache apache
```

#### **Step 2: Add Conditional Variables**
Define conditional variables in the roles’ `defaults/main.yml`:
```yaml
# nginx/defaults/main.yml
enable_nginx_lb: false
load_balancer_is_required: false
```

#### **Step 3: Configure Conditional Role Execution**
Use conditions in `loadbalancers.yml` to enable the appropriate load balancer:
```yaml
- hosts: lb
  roles:
    - { role: nginx, when: enable_nginx_lb and load_balancer_is_required }
    - { role: apache, when: enable_apache_lb and load_balancer_is_required }
```

#### **Step 4: Environment-Specific Settings**
Define environment-specific variables to control the active load balancer:
```yaml
# For Nginx
enable_nginx_lb: true
load_balancer_is_required: true

# For Apache
enable_apache_lb: true
load_balancer_is_required: true
```

---

## **5. Summary**

- **Dynamic Assignments**: Provide flexibility to handle environment-specific configurations during runtime.
- **Community Roles**: Simplify tasks like database setup with pre-built, reusable Ansible roles.
- **Conditional Logic**: Allow seamless switching between roles (e.g., Nginx vs. Apache) based on environment needs.

### **Best Practices**
1. Use static assignments for predictable tasks and dynamic assignments for runtime flexibility.
2. Leverage community roles to save time and ensure robust configurations.
3. Always test changes in a staging environment before deploying to production.
4. Maintain a clear directory structure for easy management and scalability.

---
