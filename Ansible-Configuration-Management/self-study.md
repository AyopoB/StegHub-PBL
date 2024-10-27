### Ansible Self-Study Notes

#### 1. **Introduction to Ansible**
   - **Purpose**: Open-source tool for configuration management, application deployment, and orchestration.
   - **Benefits**: Simple syntax (YAML-based playbooks), agentless architecture (only requires SSH), and supports multi-tier deployments.
   - **Core Components**:
     - **Playbooks**: YAML files that define a set of tasks for configuring systems.
     - **Inventory**: List of nodes (hosts) Ansible will manage, organized by groups or environments.

#### 2. **Key Commands in Ansible**

| Command                      | Description                                                                                     |
|------------------------------|-------------------------------------------------------------------------------------------------|
| `ansible --version`          | Check the version of Ansible installed.                                                         |
| `ansible all -m ping`        | Ping all hosts in the inventory to verify connectivity.                                         |
| `ansible-playbook playbook.yml` | Run a playbook named `playbook.yml` to execute defined tasks on specified hosts.              |
| `ansible-inventory --list`   | Display the current inventory and the hosts configured within it.                               |
| `ansible-doc -l`             | List available Ansible modules with descriptions.                                               |
| `ansible-vault create <file>` | Create a new file encrypted with Ansible Vault for storing sensitive information.              |
| `ansible-config dump`        | Display all Ansible configuration settings for troubleshooting or verification.                 |
| `ansible localhost -m setup` | Gather and display system facts about the localhost.                                           |

#### 3. **Basic Structure of a Playbook**

- **Playbook Sample**:
   ```yaml
   - name: Install and configure Apache
     hosts: webservers
     become: yes  # Run tasks with sudo privileges
     tasks:
       - name: Install Apache
         apt:
           name: apache2
           state: present
       - name: Start Apache service
         service:
           name: apache2
           state: started
           enabled: true
   ```

#### 4. **Ansible Modules**

   - **Common Modules**:
     - `apt/yum`: Install packages on Debian/RedHat-based systems.
     - `service`: Start, stop, or enable services.
     - `copy`: Copy files to remote hosts.
     - `command`/`shell`: Execute commands on remote hosts (useful for custom scripts).
     - `file`: Manage file properties (permissions, ownership).

#### 5. **Best Practices**

   - Use **YAML syntax** carefully (spacing is crucial).
   - Organize tasks into **roles** for better reusability and structure.
   - Secure sensitive data with **Ansible Vault**.
   - Structure **inventory files** by groups for environments (e.g., `[dev]`, `[prod]`).
   - Regularly **test playbooks** in a staging environment before production.