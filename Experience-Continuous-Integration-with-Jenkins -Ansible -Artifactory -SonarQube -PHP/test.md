## Ansible Roles for CI Environment

### Introduction
In this section, we will expand our Ansible setup by adding two new roles:

1. **SonarQube**: For continuous inspection of code quality.
2. **Artifactory**: For managing build artifacts.

We will also integrate Jenkins to automate Ansible deployments and set up a CI pipeline. This guide covers the step-by-step process to achieve these objectives.

---

## Why SonarQube?
SonarQube is an open-source platform developed by SonarSource for continuous code quality inspection. It performs static code analysis to detect bugs, code smells, and security vulnerabilities. By integrating SonarQube into our CI/CD pipeline, we can ensure code quality and enforce best practices. This will become more evident as we work hands-on with SonarQube and Jenkins.

### Key Features of SonarQube
- Automated code reviews.
- Detection of bugs and vulnerabilities.
- Support for multiple programming languages.

---

## Why Artifactory?
Artifactory, developed by JFrog, is a binary repository manager. It stores build artifacts, enabling version control and efficient artifact management. For our CI pipeline, Artifactory will manage the outcomes of the build process.

### Key Features of Artifactory
- Artifact storage and versioning.
- Supports integration with CI/CD tools.
- Enhances build automation.

---## Ansible Roles for CI Environment

### Introduction
In this section, we will expand our Ansible setup by adding two new roles:

1. **SonarQube**: For continuous inspection of code quality.
2. **Artifactory**: For managing build artifacts.

We will also integrate Jenkins to automate Ansible deployments and set up a CI pipeline. This guide covers the step-by-step process to achieve these objectives.

---

## Why SonarQube?
SonarQube is an open-source platform developed by SonarSource for continuous code quality inspection. It performs static code analysis to detect bugs, code smells, and security vulnerabilities. By integrating SonarQube into our CI/CD pipeline, we can ensure code quality and enforce best practices. This will become more evident as we work hands-on with SonarQube and Jenkins.

### Key Features of SonarQube
- Automated code reviews.
- Detection of bugs and vulnerabilities.
- Support for multiple programming languages.

---

## Why Artifactory?
Artifactory, developed by JFrog, is a binary repository manager. It stores build artifacts, enabling version control and efficient artifact management. For our CI pipeline, Artifactory will manage the outcomes of the build process.

### Key Features of Artifactory
- Artifact storage and versioning.
- Supports integration with CI/CD tools.
- Enhances build automation.

---
## Configuring Ansible Roles for SonarQube and Artifactory
### 1. SonarQube Role

#### Manual Setup Instructions for SonarQube
- **Install Java**: Ensure Java is installed as SonarQube requires it.
- **Download SonarQube**: Fetch the latest version from the [official site](https://www.sonarqube.org/).
- **Setup Database**: Configure a PostgreSQL database for SonarQube.
- **Run SonarQube**: Start the SonarQube service and ensure it is accessible.

#### Automating Setup with Ansible
Create a new Ansible role for SonarQube:

```bash
ansible-galaxy init roles/sonarqube
```

- **tasks/main.yml**: Define tasks to install Java, download SonarQube, configure the database, and start the service.
- **templates/**: Add configuration templates for `sonar.properties` and `wrapper.conf`.
- **defaults/main.yml**: Set default variables for SonarQube (e.g., version, ports, database credentials).

### 2. Artifactory Role

#### Manual Setup Instructions for Artifactory
- **Install JFrog Artifactory**: Download and install the application.
- **Setup Repositories**: Configure repositories to manage build artifacts.

#### Automating Setup with Ansible
Create a new Ansible role for Artifactory:

```bash
ansible-galaxy init roles/artifactory
```

- **tasks/main.yml**: Define tasks to download and install Artifactory, configure repositories, and start the service.
- **defaults/main.yml**: Set default variables such as Artifactory version and configuration paths.

---

## Configuring Jenkins for Ansible Deployment
To streamline Ansible command execution, we will integrate Jenkins. This allows running Ansible commands directly from the Jenkins UI.

### Steps to Configure Jenkins

#### 1. Install Blue Ocean Plugin
1. Navigate to the Jenkins dashboard.
2. Go to **Manage Jenkins > Plugins**.
3. Search for and install the **Blue Ocean** plugin.

#### 2. Create a New Pipeline
1. Open Blue Ocean from the Jenkins dashboard.
2. Click **Create a new pipeline**.
3. Connect Jenkins to your GitHub repository:
   - Generate a GitHub Access Token.
   - Copy and paste the token in Jenkins to establish the connection.

#### 3. Add a Jenkinsfile to the Ansible Project
1. Inside your Ansible project, create a new directory named `deploy`.
2. Add a `Jenkinsfile` in the `deploy` directory with the following content:

```groovy
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                script {
                    sh 'echo "Building Stage"'
                }
            }
        }
    }
}
```

3. In Jenkins, navigate to your pipeline configuration and specify the path to the `Jenkinsfile` as `deploy/Jenkinsfile`.

#### 4. Trigger a Build
1. Go back to the pipeline and click **Build Now**.
2. Review the console output to ensure the pipeline runs successfully.

#### 5. Explore Blue Ocean
- Open Blue Ocean and trigger builds from its interface.
- Notice how the multibranch pipeline feature scans all branches in your repository.

---

## Expanding the Pipeline

### Add More Stages
1. Create a new branch:

```bash
git checkout -b feature/jenkinspipeline-stages
```

2. Update the `Jenkinsfile` to include additional stages:

```groovy
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                script {
                    sh 'echo "Building Stage"'
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    sh 'echo "Testing Stage"'
                }
            }
        }
        stage('Package') {
            steps {
                script {
                    sh 'echo "Packaging Stage"'
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    sh 'echo "Deploying Stage"'
                }
            }
        }
        stage('Clean Up') {
            steps {
                script {
                    sh 'echo "Cleaning Up"'
                }
            }
        }
    }
}
```

3. Push changes to GitHub and scan the repository in Jenkins to detect the new branch.
4. Verify the pipeline stages in Blue Ocean.

### Merge Changes
1. Create a pull request to merge the `feature/jenkinspipeline-stages` branch into `main`.
2. Switch to the `main` branch and pull the latest changes:

```bash
git checkout main
git pull origin main
```

3. Verify that the pipeline in Blue Ocean reflects all stages.

---

## Final Pipeline Overview
Your final pipeline should include the following stages:
1. **Build**
2. **Test**
3. **Package**
4. **Deploy**
5. **Clean Up**

Each stage should display its output in Blue Ocean, confirming a successful end-to-end CI pipeline integration with Jenkins and Ansible.








### 3. Configure the Plot Plugin
Use the Plot plugin to graphically display `phploc` data in Jenkins. Add the following stage to the `Jenkinsfile`:

```groovy
stage('Plot Code Coverage Report') {
    steps {
        plot csvFileName: 'phploc-report.csv',
             csvSeries: [[
                 displayTableFlag: false,
                 exclusionValues: 'Lines of Code (LOC),Comment Lines of Code (CLOC),Non-Comment Lines of Code (NCLOC),Logical Lines of Code (LLOC)',
                 file: 'build/logs/phploc.csv',
                 inclusionFlag: 'INCLUDE_BY_STRING',
                 url: ''
             ]],
             group: 'phploc',
             numBuilds: '100',
             style: 'line',
             title: 'Lines of Code Report',
             yaxis: 'Lines of Code'
    }
}