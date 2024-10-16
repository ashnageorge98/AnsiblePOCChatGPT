# This is a project obtained from ChatGPT so named it AnsiblePOCChatGPT
To master Ansible for CI/CD in DevOps, Cloud Engineering, or SRE roles, a comprehensive project with detailed steps is essential. Here's a real-time project designed to help you develop skills in automation, configuration management, and CI/CD pipeline creation using Ansible. This project covers setting up infrastructure, deploying applications, and automating the CI/CD pipeline with Ansible.
Project: Automated Deployment of a Web Application using Ansible and Jenkins
Overview:
You will automate the deployment of a simple web application on multiple servers using Ansible, integrated with Jenkins for CI/CD automation. This setup can be used to showcase your skills for cloud engineering, SRE, or DevOps roles.
________________________________________
Step 1: Set Up Infrastructure (Local or Cloud)
Set up the infrastructure where you'll deploy the web application. You can use local VMs or cloud servers (AWS, Azure, GCP).
a. Create 3 nodes:
•	Controller Node: The machine where Ansible and Jenkins will run.
•	Target Nodes (2): These are the machines (or VMs) where the web application will be deployed.
You can create the VMs using Terraform or manual setup. If you're using AWS, launch 3 EC2 instances:
•	Controller: t2.micro, Ubuntu 20.04.
•	Web Servers: 2 instances, t2.micro, Ubuntu 20.04.
Commands for manual VM setup on AWS:
bash
Copy code
# Launch instance
aws ec2 run-instances --image-id ami-0abcdef1234567890 --count 3 --instance-type t2.micro --key-name MyKeyPair

# Install necessary software
sudo apt update && sudo apt install python3 ansible sshpass git -y
________________________________________
Step 2: Install Ansible and Jenkins on the Controller Node
1.	Install Ansible on your controller node:
bash
Copy code
sudo apt update
sudo apt install ansible -y
2.	Install Jenkins:
bash
Copy code
sudo apt update
sudo apt install openjdk-11-jdk -y
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt install jenkins -y
sudo systemctl start jenkins
sudo systemctl enable jenkins
3.	Access Jenkins through http://<ControllerNodeIP>:8080 and complete the initial setup.
________________________________________
Step 3: Configure Ansible Inventory and SSH Access
1.	Create an Ansible inventory file with the target nodes:
bash
Copy code
vim /etc/ansible/hosts
Add:
ini
Copy code
[webservers]
192.168.1.2
192.168.1.3
Replace the IPs with your target nodes.
2.	Set up SSH access from the Ansible controller to the target nodes:
bash
Copy code
ssh-keygen -t rsa
ssh-copy-id ubuntu@192.168.1.2
ssh-copy-id ubuntu@192.168.1.3
3.	Test Ansible connectivity:
bash
Copy code
ansible -m ping all
________________________________________
Step 4: Create Ansible Playbook to Install Web Server
1.	Create the playbook that installs and starts an NGINX web server on the target nodes.
File: deploy_nginx.yml
yaml
Copy code
---
- hosts: webservers
  become: true
  tasks:
    - name: Install NGINX
      apt:
        name: nginx
        state: present
        update_cache: yes

    - name: Start NGINX service
      service:
        name: nginx
        state: started
2.	Run the playbook:
bash
Copy code
ansible-playbook -i inventory.ini deploy_nginx.yml________________________________________
Step 5: Create Jenkins Pipeline for CI/CD Automation
1.	Install Ansible Plugin in Jenkins:
o	Go to Jenkins → Manage Jenkins → Manage Plugins → Available → Search for "Ansible Plugin" and install it.
2.	Set up Jenkins pipeline to trigger Ansible playbooks automatically.
Create a new Jenkins pipeline job:
o	Go to Jenkins → New Item → Pipeline.
3.	Create Jenkinsfile: This pipeline pulls the latest code from GitHub and deploys the web app using Ansible.
Add the following in your Jenkinsfile:
groovy
Copy code
pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/yourusername/ansible-web-deploy.git'
            }
        }

        stage('Run Ansible Playbook') {
            steps {
                ansiblePlaybook playbook: 'deploy_nginx.yml', inventory: 'hosts'
            }
        }
    }
}
4.	Configure GitHub Webhook:
o	Set up a GitHub webhook to trigger the Jenkins pipeline on code push:
	Go to your GitHub repository → Settings → Webhooks → Add Webhook.
	Add your Jenkins URL http://<controller-ip>:8080/github-webhook/ and select "Just the push event."
________________________________________
Step 6: Test the End-to-End CI/CD Pipeline
1.	Push a code change to your GitHub repository (e.g., modify the index.html).
2.	The Jenkins pipeline should trigger automatically, pulling the changes and running the Ansible playbook to redeploy the web application.
3.	Verify that the web application is running by accessing the target node IPs in your browser.
________________________________________
Step 7: Extend the Project (Optional Enhancements)
1.	Database Setup: Automate the installation of MySQL or PostgreSQL in the playbook.
2.	Rolling Updates: Implement rolling updates to avoid downtime during deployments.
3.	Monitoring and Alerting: Use Ansible to configure monitoring tools like Prometheus or Grafana on the servers.
4.	AWS Integration: Automate the creation of EC2 instances using Ansible and deploy the app on them.
5.	Configuration Management: Use Ansible to manage configuration files (e.g., NGINX, MySQL config files) across multiple environments.
________________________________________
Skills You'll Master:
1.	Ansible for Configuration Management
2.	Jenkins for CI/CD Automation
3.	Infrastructure Setup (on AWS or VMs)
4.	Web Server Automation (NGINX)
5.	GitHub Integration (Webhooks for CI/CD)
6.	Real-time Deployments across multiple servers
This project will give you real-world exposure to Ansible’s role in CI/CD pipelines, which is highly valuable in DevOps, Cloud Engineering, or SRE roles.

