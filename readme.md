# Web Server Automation with Jenkins and Ansible

![CI/CD Pipeline](https://img.shields.io/badge/CI%2FCD-Pipeline-brightgreen)
![Jenkins](https://img.shields.io/badge/Jenkins-Automation-red)
![Ansible](https://img.shields.io/badge/Ansible-Deployment-blue)
![Apache](https://img.shields.io/badge/Apache-HTTPD-orange)

This project demonstrates a complete CI/CD pipeline that automates the deployment of web content to Apache (httpd) servers using Jenkins and Ansible. The pipeline automatically triggers when changes are pushed to this repository, deploying the updated web content and restarting the web service.

## 📋 Table of Contents

- [Overview](#-overview)
- [Repository Structure](#-repository-structure)
- [Prerequisites](#-prerequisites)
- [Setup Instructions](#-setup-instructions)
  - [Jenkins Configuration](#jenkins-configuration)
  - [Ansible Setup](#ansible-setup)
  - [GitHub Integration](#github-integration)
- [How It Works](#-how-it-works)
- [Manual Execution](#-manual-execution)
- [Troubleshooting](#-troubleshooting)
- [Contributing](#-contributing)

## 🌐 Overview

This automation solution delivers:

- **Continuous Deployment**: Web content is automatically deployed when changes are pushed
- **Infrastructure as Code**: Server configuration managed through Ansible
- **Centralized Orchestration**: Jenkins coordinates the entire workflow
- **Service Management**: Apache httpd service is restarted automatically after deployment

## 📁 Repository Structure

```
.
├── index.html                 # Custom HTML homepage for the web server
├── webserver-update.yml       # Ansible playbook for deployment
└── README.md                  # This documentation file
```

## 🔧 Prerequisites

Before setting up this solution, ensure you have:

- **Jenkins** server installed and running
- **Ansible** installed on the Jenkins server
- **Git** installed on the Jenkins server
- **Apache (httpd)** installed on target servers
- **SSH access** from Jenkins to target servers
- Proper **sudo permissions** for the Jenkins user

## 🚀 Setup Instructions

### Jenkins Configuration

1. **Create a new Jenkins job**:
   - Select "Freestyle project"
   - Configure Source Code Management (SCM):
     - Select Git
     - Enter this repository URL
     - Add credentials if needed

2. **Configure Build Triggers**:
   - Option 1: **Poll SCM** (recommended for simple setups)
     ```
     H/2 * * * *    # Checks for updates every 2 minutes
     ```
   - Option 2: **GitHub hook trigger** (for immediate builds)
     - Check "GitHub hook trigger for GITScm polling"

3. **Build Step**:
   - Add build step: "Invoke Ansible Playbook"
   - Playbook path: `/etc/ansible/playbooks/webserver-update.yml`
   - Advanced options:
     - Add extra variable: `ansible_become_pass` with Jenkins sudo password

4. **Email Notifications** (optional):
   - Add post-build action: "Email Notification"
   - Configure recipients and notification criteria

### Ansible Setup

1. **Playbook placement**:
   ```bash
   # After cloning this repository
   sudo mv webserver-update.yml /etc/ansible/playbooks/
   ```

2. **Inventory setup**:
   - Ensure your target servers are defined in Ansible inventory
   - Default location: `/etc/ansible/hosts`

3. **SSH key setup**:
   - Jenkins user needs SSH access to target servers
   - Set up passwordless SSH or provide credentials in Jenkins

### GitHub Integration

For automatic webhook triggering (optional but recommended):

1. **In GitHub repository**:
   - Go to Settings → Webhooks → Add webhook
   - Payload URL: `http://<jenkins-server>:8080/github-webhook/`
   - Content type: `application/json`
   - Secret: (optional)
   - Enable "Just the push event"

2. **For private networks**:
   - Use ngrok or a similar service to expose Jenkins
   - Command: `ngrok http 8080`
   - Use the generated URL in GitHub webhook configuration

## ⚙️ How It Works

1. **Code Push**: Developer pushes changes to the index.html file or playbook
2. **Trigger**: Jenkins detects changes via polling or webhook
3. **Checkout**: Jenkins pulls the latest code from the repository
4. **Deployment**: Jenkins executes the Ansible playbook
5. **Execution**: Ansible:
   - Copies the updated index.html to target servers
   - Restarts the httpd service to apply changes
6. **Notification**: (Optional) Email sent based on job success/failure

## 🖥️ Manual Execution

To manually deploy changes without Jenkins:

```bash
# Clone the repository
git clone https://github.com/anuragstark/Anible-jenkins-deployment-.git

# Move the playbook (if not already done)
sudo mv webserver-update.yml /etc/ansible/playbooks/

# Run the playbook
ansible-playbook /etc/ansible/playbooks/webserver-update.yml -i inventory.ini
```

## 🔍 Troubleshooting

- **Jenkins cannot access Git**: Check credentials and network connectivity
- **Ansible fails**: Verify SSH access and sudo permissions
- **Webhook not working**: Check firewall settings or use ngrok for local Jenkins
- **Service not restarting**: Verify httpd service name and permissions

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch: `git checkout -b feature/amazing-feature`
3. Commit your changes: `git commit -m 'Add some amazing feature'`
4. Push to the branch: `git push origin feature/amazing-feature`
5. Open a Pull Request
