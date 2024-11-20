

---

# **Ansible Dynamic Assignments and Community Roles**

## **Overview**

This project focuses on configuring UAT (User Acceptance Testing) servers with Ansible, introducing **dynamic assignments** using the `include` module and leveraging **community roles** from Ansible Galaxy. The main objectives are:

1. Understanding the difference between **static** and **dynamic** assignments.
2. Organizing Ansible files for better management of environment-specific configurations.
3. Integrating community roles to avoid reinventing common tasks, like MySQL configuration.
4. Introducing conditional logic for load balancer role selection between **Nginx** and **Apache**.

**Note:** Ansible is actively developed software. For the latest module information, visit the [Ansible Documentation](https://docs.ansible.com/).

---

## **Dynamic Assignments Using Include**

In Ansible, we differentiate between **static** and **dynamic** assignments. Here’s the breakdown:

- **Static Assignments** (using `import`): Ansible processes all referenced playbooks when the main playbook (`site.yml`) is parsed. This means any changes made to the statements after parsing will not be considered, making it static.
- **Dynamic Assignments** (using `include`): Statements are processed only during playbook execution. This allows for any changes to take effect in real time.

**Recommendation:** Use static assignments (`import`) for reliability and ease of debugging. Use dynamic assignments (`include`) selectively, especially for environment-specific variables.

### **Project Structure**

1. **Create a New Branch**:
   Start by creating a new branch in your GitHub repository named **dynamic-assignments**:
   ```bash
   git checkout -b dynamic-assignments
   ```

   ![](img/git%20new%20branch.png)

2. **Create Directory for Dynamic Assignments**:
   Within this branch, add a folder `dynamic-assignments` to store dynamic configurations.

   ```
   ├── dynamic-assignments
   │   └── env-vars.yml
   ```

3. **Configure Environment-Specific Variables**:
   Create an `env-vars.yml` file inside the `dynamic-assignments` folder. This file will collate environment-specific variables dynamically during playbook execution.

4. **Setup Environment Variables Directory**:
   Create a new folder named **env-vars** to hold YAML files for each environment:
   - `dev.yml`
   - `stage.yml`
   - `uat.yml`
   - `prod.yml`

   ```
   ├── env-vars
   │   ├── dev.yml
   │   ├── stage.yml
   │   ├── uat.yml
   │   └── prod.yml
   ```

   - Our folder structure now looks like this:
   ```
   ├── dynamic-assignments
   │   └── env-vars.yml
   ├── env-vars
       └── dev.yml
       └── stage.yml
       └── uat.yml
       └── prod.yml
   ├── inventory
       └── dev
       └── stage
       └── uat
       └── prod
   ├── playbooks
       └── site.yml
   └── static-assignments
       └── common.yml
       └── webservers.yml
   ```

   ![](img/folder%20structure.png)


5. **Edit `env-vars.yml` for Dynamic Assignment**:
   Insert the following code in `env-vars.yml` to use dynamic assignments:
```yaml
---
- name: Collate variables from environment-specific file
  include_vars:
    file: "{{ item }}"
  with_first_found:
    - files:
        - "{{ inventory_file | basename | splitext | first }}.yml"
        - dev.yml
        - staging.yml
        - prod.yml
        - uat.yml
      paths:
        - "{{ playbook_dir }}/../env-vars"
  tags:
    - always
```

   **Explanation**:
   - **`include_vars`**: Dynamically includes environment-specific variables.
   - **`playbook_dir`**: Refers to the playbook’s directory for accessing files.
   - **`with_first_found`**: Ensures that the first available file (matching environment) is selected.

   Notice 3 things to notice here:

     1. We used include_vars syntax instead of include, this is because Ansible developers decided to separate different features of the module. From Ansible version 2.8, the include module is deprecated and variants of include_* must be used. These are:
      - include_role
      - include_tasks
      - include_vars
   In the same version, variants of import were also introduces, such as:

      - import_role
      - import_tasks

   2. We made use of a special variables {{ playbook_dir }} and {{ inventory_file }}. {{ playbook_dir }} will help Ansible to determine the location of the running playbook, and from there navigate to other path on the filesystem. {{ inventory_file }} on the other hand will dynamically resolve to the name of the inventory file being used, then append .yml so that it picks up the required file within the env-vars folder.
   3. We are including the variables using a loop. with_first_found implies that, looping through the list of files, the first one found is used. This is good so that we can always set default values in case an environment specific env file does not exist.





6. **Update `site.yml` with Dynamic Assignments**:
   Add the `include` directive in `site.yml` to use dynamic variables:
   ```yaml
   ---
   - hosts: all
     - name: Include dynamic variables
       tasks:
         import_playbook: ../static-assignments/common.yml
         include: ../dynamic-assignments/env-vars.yml
         tags: - always
   - import_playbook: ../static-assignments/uat-webservers.yml
   ```

![](img/update%20siteyml.png)

git commit to add new changes to githu directory

![](img/1st%20git%20commit.png)
---

## **Community Roles**

For repetitive tasks like setting up a MySQL database, we can leverage community roles available on [Ansible Galaxy](https://galaxy.ansible.com/).

### **Adding the MySQL Role**

1. **Initialize Git and Branch for Roles**:
   ```bash
   git init
   git pull https://github.com/<your-name>/ansible-config-mgt.git
   git remote add origin https://github.com/<your-name>/ansible-config-mgt.git
   git branch roles-feature
   git switch roles-feature
   ```

   ![](img/git%20init.png)

   ![](img/git%20init%20b.png)

2. **Install MySQL Role from Galaxy**:
   Use the following command to install the MySQL role:
   ```bash
   ansible-galaxy install geerlingguy.mysql
   ```
   Rename the role folder:
   ```bash
   mv geerlingguy.mysql/ mysql
   ```

3. **Edit the Role Variables**:
   In the `roles/mysql/vars/main.yml` file, set up your database configuration, such as the root password, database name, and user credentials:
```yaml
mysql_root_password: ""

mysql_databases:
  - name: "( input your required db name )"
    encoding: latin1
    collation: latin1_general_ci

mysql_users:
  - name: "( include your required db user name )"
    host: "( include the required subnet cidr ip address of your webservers )"
    password: "( include your required password for the db )"
    priv: "(include the added db name).*:ALL"

```
    - Follow the instructions in the `README.md` file inside the `mysql` role folder to ensure proper setup for your MySQL instance.
   - You might need to tweak the configuration depending on your tooling website requirements.


4. **Push Changes to GitHub**:
   ```bash
   git add .
   git commit -m "Added MySQL role configuration"
   git push --set-upstream origin roles-feature
   ```


- Save. Create a new playbook inside static-assignments folder and call it db-servers.yml , update it with the created roles. use the code below:

```yaml
---
- hosts: db
  become: yes
  vars_files:
    - vars/main.yml
  roles:
    - role: mysql
```

Now, if you are satisfied with your codes, you can create a Pull Request on GitHub to merge your `roles-feature` branch into the `main` branch.

---

## **Configuring Load Balancer Roles**

To allow flexibility in choosing between **Nginx** and **Apache** load balancers:

1. **Create Load Balancer Roles**:
   - Develop or install community roles for `nginx` and `apache` from Ansible Galaxy.

   1. **Install Nginx Load Balancer Role**:
   ```bash
   ansible-galaxy install geerlingguy.nginx
   mv geerlingguy.nginx nginx
   ```

2. **Install Apache Load Balancer Role**:
   ```bash
   ansible-galaxy install geerlingguy.apache
   mv geerlingguy.apache apache
   ```

> NB: Since only one load balancer (either Nginx or Apache) will be enabled in each environment, we will use conditional logic in our roles.

   
2. **Setup Conditional Variables**:
   In the `defaults/main.yml` file inside both the `nginx` and `apache` roles, define variables to control whether the load balancer should be enabled:

   ```yaml
   # roles/nginx/defaults/main.yml
   enable_nginx_lb: false
   load_balancer_is_required: false

   # roles/apache/defaults/main.yml
   enable_apache_lb: false
   load_balancer_is_required: false
   ```

3. **Configure Load Balancer Assignments**:
   Create a `loadbalancers.yml` file, Use conditional role execution to decide whether to enable Nginx or Apache based on environment variables:
   ```yaml
   - hosts: lb
     roles:
       - { role: nginx, when: enable_nginx_lb and load_balancer_is_required }
       - { role: apache, when: enable_apache_lb and load_balancer_is_required }
   ```

4. **Update `site.yml` to Import `loadbalancers.yml`**:
   Insert the following:
   ```yaml
   - name: Load Balancers assignment
     hosts: lb
     tasks:
       - import_playbook: ../static-assignments/loadbalancers.yml
         when: load_balancer_is_required
   ```

5. **Environment-Specific Configuration**:
   In each `env-vars` file, enable either `nginx` or `apache`:
   ```yaml
   enable_nginx_lb: true
   load_balancer_is_required: true
   ```

   
   To switch to Apache, set:
   ```yaml
   enable_nginx_lb: false
   enable_apache_lb: true
   load_balancer_is_required: true
   ```

6. In the role/nginx/defaults/main.yml file, uncomment the nginx_vhosts, and nginx_upstream section:
```bash
nginx_vhosts:
# Example vhost below, showing all available options:
 - listen: "80" # default: "80"
   server_name: "example.com" # default: N/A
   root: "/var/www/html" # default: N/A
   index: "index.php index.htm" # default: "index.html index.htm"
#   filename: "example.com.conf" # Can be used to set the vhost filename.

    locations:
              - path: "/"
                proxy_pass: "http://myapp1"

#   # Properties that are only added if defined:
   server_name_redirect: "www.example.com" # default: N/A
   error_page: ""
   access_log: ""
   error_log: ""
   extra_parameters: "" # Can be used to add extra config blocks (multiline).
   template: "{{ nginx_vhost_template }}" # Can be used to override the `nginx_vhost_template` per host.
   state: "present" # To remove the vhost configuration.
   ```
7.Under the nginx_upstream section, you wil need to update the servers address to include your webservers or uat servers.:
```bash
nginx_upstreams:
  - name: myapp1
    strategy: "ip_hash" # "least_conn", etc.
    keepalive: 16 # optional
    servers:
      - "<uat-server2-ip-address> weight=5"
      - "<uat-server1-ip-address> weight=5"
```

finally, update the inventory/uat.yml to include the neccesary details for ansible to connect to each of these servers to perform all the roles we have specified. use the code below :
```bash
[db]
172.31.83.187 ansible_ssh_user=ubuntu

[lb]
172.31.86.165 ansible_ssh_user=ubuntu
```
---

## **Testing and Validation**

1. **Setup Inventory for Each Environment**:
   Update inventory files to include environment-specific servers.

1. **Check for Syntax Errors**:
   Before running the playbook, check for syntax errors:
   ```bash
   ansible-playbook site.yml --syntax-check
   ```

   1. **Dry Run the Playbook**:
   Run a dry run to see what changes would be applied without making any actual modifications:
   ```bash
   ansible-playbook site.yml -C -i inventory/uat
   ```

2. **Run Playbooks**:
   Execute playbooks for different environments to validate that variables and roles are correctly applied.

   ```bash
   ansible-playbook -i inventory/uat.yml playbooks/site.yml
   ```

4. **Commit the Updated Roles**:
   After testing, commit the changes to GitHub:
   ```bash
   git add .
   git commit -m "Add MySQL and load balancer roles with conditional execution"
   git push origin roles-feature
   ```

5. **Merge Pull Request**:
   On GitHub, create a Pull Request to merge the `roles-feature` branch into `main` and review the changes.
---

## **Conclusion**

In this project, we utilized **dynamic assignments** to configure environment-specific variables, integrated **community roles** for MySQL, and implemented conditional load balancer roles for Nginx and Apache. This approach demonstrates modular configuration management in Ansible, enabling scalability and flexibility across environments.

--- 








---
# Play 1: Loads dynamic variables for all hosts
- name: Include dynamic variables
  hosts: all
  tasks:
    - name: Load dynamic variables
      include_tasks: ../dynamic-assignments/env-vars.yml
      tags:
        - always

# Play 2: Imports common configuration for all hosts
- name: Import common playbook
  import_playbook: ../static-assignments/common.yml

# Play 3: Specific configuration for UAT webservers only
- name: Configure UAT webservers
  hosts: uat-webservers
  tasks:
    - name: Import UAT specific tasks
      import_tasks: ../static-assignments/uat-webservers.yml