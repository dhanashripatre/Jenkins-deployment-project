# DevOps CI/CD Pipeline Project – Automated Website Deployment Using Jenkins & Docker
<img src="https://github.com/dhanashripatre/Jenkins-deployment-project/blob/master/images1/architecture.png" width="900">

## 📌 Project Title
Automated CI/CD Pipeline for Website Deployment Using Jenkins, Docker, GitHub Webhooks, and Multi-Node Jenkins Architecture

---

## 📖 Project Overview
This project demonstrates a complete DevOps CI/CD pipeline where a static website is developed on a Development Server, pushed to GitHub, and automatically deployed using Jenkins and Docker on multiple Jenkins nodes.

The pipeline uses:
- GitHub for source code management
- Jenkins for automation
- Docker for containerization
- Jenkins Master-Agent architecture for distributed builds
- GitHub Webhooks for automatic triggering

The website is deployed automatically whenever code changes are pushed to GitHub.

---

## 🏗️ Architecture

```text
Developer Changes
       │
       ▼
 Dev Server
       │
       ▼
    GitHub
       │
       ▼
 GitHub Webhook
       │
       ▼
 Jenkins Master
       │
 ┌─────┴─────┐
 ▼           ▼
Agent 1    Agent 2
(Docker)   (Docker)
       │
       ▼
 Website Containers
```

---

## 🖥️ Infrastructure Used

| Server              | Purpose                                  |
|---------------------|------------------------------------------|
| Dev Server          | Website development and Git operations   |
| Jenkins Master      | CI/CD automation server                  |
| Jenkins Slave/Agent | Distributed deployment/build node        |

---

## ⚙️ Technologies Used

1. Ubuntu Server 24.04 LTS  
2. Jenkins  
3. Docker  
4. Git & GitHub  
5. Apache2  
6. GitHub Webhooks  

---

## 📂 Project Workflow

1. The developer downloads and modifies the website source code on the Dev Server.  
2. The code is pushed to the GitHub repository.  
3. The GitHub Webhook triggers the Jenkins job automatically.  
4. Jenkins pulls the latest source code.  
5. Jenkins builds the Docker image.  
6. The Docker container is deployed on Jenkins nodes.  
7. The updated website becomes live automatically.  

---

## 💻 Setup

---

## Step 1 – Setup Development Server

## ☁️ Create EC2 Development Server

Login to AWS Console:  
AWS Console → EC2 → Launch Instance

---

## 🖥️ Instance Configuration

| Setting | Value |
|----------|-------|
| Instance Name | dev-server |
| AMI | Ubuntu Server 24.04 LTS |
| Instance Type | t2.micro (Free Tier Eligible) |
| Storage | 8 GB SSD |
| Key Pair | Create or select an existing key pair |
| Security Group | Allow SSH (Port 22) |

---

## 🔐 Connect to EC2 Instance

After launching the instance, connect using SSH:

```bash
ssh -i <your-key.pem> ubuntu@<public-ip>
```

OR

Use PuTTY to connect:

```text
HostName - ubuntu@<public_ip_of_instance>
```

- Go to SSH → Auth → Credentials
- Add your private key in `.ppk` format under the private key file for authentication
- Connect to the server and accept the conditions

Switch to the root user:

```bash
sudo su -
```

---

## 📥 Download Website Template

```bash
cd /root
wget https://www.tooplate.com/zip-templates/2130_waso_strategy.zip
apt-get update -y
apt install unzip -y
unzip 2130_waso_strategy.zip
```

---

## 📂 Move to Project Directory

```bash
cd /root/2130_waso_strategy
```

---

## 🐳 Create Dockerfile

```Dockerfile
FROM ubuntu
RUN apt-get update -y
RUN apt-get install -y apache2
COPY . /var/www/html
CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
```

---

## 🐱 Create a GitHub Repository

Create a repository and give it a name.

<img src="https://github.com/dhanashripatre/Jenkins-deployment-project/blob/master/images1/Screenshot%202026-05-12%20230957.png" width="900">

---

## 🔧 Initialize Git Repository

Install Git if it is not installed:

```bash
apt install git -y
```

Initialize the repository:

```bash
git init
git add *
git commit -m "Initial commit Waso Strategy Website"
git remote add origin https://github.com/<your-username>/webserver-repo.git
git push -u origin master
```

If it asks for:
- Username → `<your_username>`
- Password → `<classic_token>`

---

## Steps to Create a Classic Token

Step 1 – Login to GitHub

Step 2 – Open Developer Settings

Profile Picture → Settings → Developer Settings

Step 3 – Open Personal Access Tokens

Personal Access Tokens → Tokens (Classic)

Step 4 – Generate New Token

Generate New Token → Generate New Token (Classic)

Step 5 – Configure Token

  Give a name such as:
  - jenkins-token

  Expiration:
  Choose:
  - 90 days
  - No expiration

  Select Scopes:
  - Enable all permissions

Step 6 – Generate Token

GitHub will show the token only once. Copy and save it immediately.

---

## 🚀 Step 2 – Setup Jenkins Master

- Login to AWS Console:
AWS Console → EC2 → Launch Instance

- Use the same configuration as the Dev Server

- In the security group, open port 80 (HTTP) and 8080 (Jenkins port)

- After launching the instance, connect using SSH:

```bash
ssh -i <your-key.pem> ubuntu@<public-ip>
```

---

## Run These Commands

```bash
apt update -y
apt install fontconfig openjdk-21-jre -y
wget -O /etc/apt/keyrings/jenkins-keyring.asc https://pkg.jenkins.io/debian-stable/jenkins.io-2026.key

echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null

apt update -y
apt install jenkins -y
systemctl start Jenkins
```

---

## Configure Passwordless Sudo for Jenkins

```bash
vim /etc/sudoers
```

Add:

```text
jenkins ALL=(ALL) NOPASSWD: ALL
```

---

## Install Git (Optional)

```bash
apt install git -y
```

---

## Install Docker on Jenkins Master

```bash
apt install docker.io -y
systemctl start docker
systemctl enable docker
```

---

## Open Jenkins in Browser

```text
http://<public_ip>:8080
```

- Create a Jenkins user

<img src="https://github.com/dhanashripatre/Jenkins-deployment-project/blob/master/images1/jenkins%20user%20page.png" width="700">

---

## 🚀 Step 3 – Configure GitHub Webhook

Go to the GitHub Repository:

<img src="https://github.com/dhanashripatre/Jenkins-deployment-project/blob/master/images1/github_settings.png" >

```text
Settings → Webhooks → Add Webhook
```

Webhook URL:

```text
http://<Jenkins-Master-Public-IP>:8080/github-webhook/
```

Content Type:

```text
application/json
```

---

## 🚀 Step 4 – Create Jenkins Job

<img src="https://github.com/dhanashripatre/Jenkins-deployment-project/blob/master/images1/job%20creation.png" width="800">

---

## Job Configuration

- Source Code Management
- Select Git
- Enter GitHub repository URL

<img src="https://github.com/dhanashripatre/Jenkins-deployment-project/blob/master/images1/github%20config.png" width="800">

---

## Build Triggers

Enable:

```text
GitHub hook trigger for GITScm polling
```

<img src="https://github.com/dhanashripatre/Jenkins-deployment-project/blob/master/images1/multi%20config%20labels.png" width="300" height="600">

---

## Add This in Execute Shell

```bash
sudo docker rm -f container1 || true
sudo docker build -t webserver-image .
sudo docker run -itd --name container1 -p 5000:80 webserver-image
```

<img src="https://github.com/dhanashripatre/Jenkins-deployment-project/blob/master/images1/jenkins%20execute%20shell.png" width="600">

- Apply and save
- Build the job, or modify something and push the code so the job is triggered automatically

---

## 🌐 Access Website

```text
http://<Jenkins-Master-Public-IP>:5000
```

---

## 🚀 Step 5 – Test CI/CD Pipeline

## Modify Website

```bash
vim index.html
```

## Push Changes

```bash
git add *
git commit -m "Updated index.html"
git push origin master
```

- The Jenkins job should trigger automatically

---

## 🚀 Step 6 – Setup Jenkins Agent/Worker Node

- Launch another instance for the Jenkins worker node
- Keep the same configuration as the Jenkins Master

---

## Node 1 (Worker Node 1)

Run these commands:

```bash
apt update -y
apt install fontconfig openjdk-21-jre -y
apt install git -y
apt install docker.io -y
systemctl start docker
systemctl enable docker
```

---

## 🔗 Connecting Jenkins Agent

From Jenkins Master Dashboard:

```text
Manage Jenkins → Nodes → New Node
```

<img src="https://github.com/dhanashripatre/Jenkins-deployment-project/blob/master/images1/worker_node.png" width="600">

---

## Configure

1. Permanent Agent  
2. Remote root directory  
3. Labels  
4. Launch method (JNLP/SSH)  

---

## In Jenkins Node1 Terminal

Create a directory and paste the same directory path into the Remote Root Directory field.

<img src="https://github.com/dhanashripatre/Jenkins-deployment-project/blob/master/images1/woeker_node_config.png" width="300" height="600">

<img src="https://github.com/dhanashripatre/Jenkins-deployment-project/blob/master/images1/curl_download_jnlp.png" >

Copy and paste the curl commands shown above into the Linux terminal.

The JNLP method was not working for me because my IP address and Jenkins URL IP were different. If you face the same issue, change the default Jenkins URL to the IP address you are currently using.

<img src="https://github.com/dhanashripatre/Jenkins-deployment-project/blob/master/images1/jnlp%20error.png" width="500" height="500">

Connect Node1 and bring it online.

Add the labels to your multi-configuration job.

<img src="https://github.com/dhanashripatre/Jenkins-deployment-project/blob/master/images1/multi%20config%20labels.png" width="300" height="600">

## Access Containers Running on Both Nodes

Paste the IP address and the port on which the container is running.

Example:

```text
http://<Jenkins-Master-Public-IP>:5000
http://<Jenkins-node1-Public-IP>:5000
```

jenkins job dashboard:-

<img src="https://github.com/dhanashripatre/Jenkins-deployment-project/blob/master/images1/jenkins%20dashboard.png" width="800">

<img src="https://github.com/dhanashripatre/Jenkins-deployment-project/blob/master/images1/jenkins_dashboard_1.png" width="800">

---

## 📚 Learning Outcomes

After completing this project, you will understand:

1. CI/CD pipeline implementation  
2. Jenkins automation  
3. Docker container deployment  
4. GitHub integration with Jenkins  
5. Webhook-based automation  
6. Distributed Jenkins architecture  
7. Production-style deployment workflow  

---

## Issues I Faced While Making This Project

---

## Problem 1

<img src="https://github.com/dhanashripatre/Jenkins-deployment-project/blob/master/images1/issue1.png">

My built-in node was offline because my `/tmp` directory did not have enough space.

---

## Solution

## Step 1

Check current mounts and check the `/tmp` space.

Use this command:

```bash
df -h
```

<img src="https://github.com/dhanashripatre/Jenkins-deployment-project/blob/master/images1/solution1.png" width="600">

---

## Step 2

Check if `/tmp` is mounted separately:

```bash
mount | grep /tmp
```

<img src="https://github.com/dhanashripatre/Jenkins-deployment-project/blob/master/images1/solution.png" width="600">

If you see `tmpfs` on `/tmp`, then run this command:

```bash
sudo umount /tmp
```

If it says “target is busy”, then run this command:

Here I stopped Jenkins:

```bash
sudo systemctl stop jenkins
```

<img src="https://github.com/dhanashripatre/Jenkins-deployment-project/blob/master/images1/solution%201(1).png" width="600">

<img src="https://github.com/dhanashripatre/Jenkins-deployment-project/blob/master/images1/solution1(2).png" width="600">

---

SOLVED

---

## Problem 2

JNLP was not working. I kept pasting the curl commands repeatedly, but it still did not work.

---

## Solution

In settings, the default Jenkins URL had the previous IP address. I changed it to the current public IP.

## Step 1

```text
Manage Jenkins → System → Jenkins URL
chnage the url to the public_ip of the jenkins_server
```

<img src="https://github.com/dhanashripatre/Jenkins-deployment-project/blob/master/images1/jnlp%20error.png" width="500" height="500">

Paste the curl commands into the terminal, and the node should come online.

---

SOLVED

Job Console Output:-

<img src="https://github.com/dhanashripatre/Jenkins-deployment-project/blob/master/images1/console_output.png" width="900">
