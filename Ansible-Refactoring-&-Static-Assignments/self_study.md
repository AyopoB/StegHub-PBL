Certainly! Here’s a `self-study-notes.md` file to summarize the key elements of this project. This outline can serve as a quick reference guide for understanding the concepts, steps, and configurations used. 

---

# Self Study Notes - Ansible Refactoring and Jenkins Job Enhancement


## Key Concepts
1. **Artifact Storage in Jenkins:**
   - Created a central directory `/home/ubuntu/ansible-config-artifact` for storing build artifacts.
   - **Permissions Update:** `chmod -R 0777` to allow Jenkins to write to this directory.
   - **Copy Artifact Plugin:** Installed and configured the Copy Artifact plugin in Jenkins for centralizing artifacts.

2. **Ansible Refactoring Using `import_playbook`:**
   - **Modular Structure:** Created an entry point playbook (`site.yml`) to import smaller, task-specific playbooks.
   - **Static Assignments Folder:** Moved playbooks like `common.yml` into `static-assignments` to keep the configuration modular.
   - **`common-del.yml` Playbook:** A deletion playbook for testing modularity by removing Wireshark from specified servers.

3. **Creating Reusable Ansible Roles:**
   - **Role Creation Using Ansible Galaxy:** Initialized a `webserver` role using `ansible-galaxy init` to standardize configuration tasks across multiple environments.
   - **Role Structure:** Followed best practices for directory structure, including tasks, handlers, templates, and other subdirectories.
   - **Webserver Role Tasks:** Defined tasks to install Apache, deploy tooling website from GitHub, start HTTPD, and perform cleanup.

4. **Configuring UAT Environment with Ansible:**
   - **Inventory Update:** Updated UAT inventory (`uat.yml`) with private IP addresses for UAT web servers and Ansible SSH user.
   - **Roles Path in `ansible.cfg`:** Ensured the correct roles path (`roles_path = /home/ubuntu/ansible-config-mgt/roles`) for Ansible to locate roles properly.

5. **Jenkins Integration with GitHub Webhooks:**
   - **Triggered by GitHub Webhook:** Ensured Jenkins jobs are triggered by webhooks, allowing automated deployment on new commits.
   - **Artifact Management:** Centralized storage reduces redundancy and maintains workspace clarity.

---

## Step-by-Step Overview

### 1. **Jenkins Artifact Centralization**
   - **Directory Creation:** `/home/ubuntu/ansible-config-artifact`
   - **Job Configuration:** Configured `save_artifacts` Jenkins job to copy artifacts from the `ansible` job upon completion.
   - **Testing:** Confirmed successful artifact centralization after updating `README.md` and committing to GitHub.

### 2. **Refactor Ansible Code for Modular Structure**
   - **New Branch:** Created `refactor` branch for modifying playbook structure.
   - **Entry Point Playbook (`site.yml`):** Acts as the main playbook and imports other playbooks.
   - **Folder Structure After Refactoring:**
      ```plaintext
      ├── static-assignments
      │   └── common.yml
      ├── inventory
      │   ├── dev
      │   ├── stage
      │   ├── uat
      │   └── prod
      └── playbooks
          └── site.yml
      ```
   - **Testing `common-del.yml`:** Verified that Wireshark is removed from target servers.

### 3. **Configuring UAT Web Servers with Webserver Role**
   - **Provision UAT Instances:** Launched `Web1-UAT` and `Web2-UAT` RHEL 9 instances for testing.
   - **Role Initialization:** Created `webserver` role using `ansible-galaxy init`.
   - **Role Tasks (main.yml):**
     - Install Apache and Git.
     - Clone tooling website from GitHub.
     - Copy HTML content to the correct location and start HTTPD service.
   - **Inventory Update:** Added UAT web servers to `uat.yml`.

### 4. **Testing and Verifying Setup**
   - **Running Playbook on UAT Servers:**
     - Ran `site.yml` targeting UAT inventory to configure UAT servers with tooling website.
   - **Browser Verification:** Accessed the tooling website using the public IPs of the UAT web servers.

---

## Lessons Learned
1. **Modularization Improves Maintenance:** Refactoring playbooks using imports (`import_playbook`) and roles (`webserver`) enables a more manageable configuration structure, making it easier to update and troubleshoot.
2. **Centralized Artifact Storage:** Reduces clutter and improves workspace efficiency in Jenkins, especially as builds grow in complexity.
3. **Roles Enhance Reusability:** Roles like `webserver` create reusable configurations, enabling quick setup for similar server environments across different stages (e.g., UAT, staging, production).
4. **GitHub Integration with Jenkins:** Leveraging webhooks for Jenkins jobs ensures an automated and responsive CI/CD pipeline.

---
