
# 🤖 Agentic DevOps Lab

A beginner-friendly AWS and DevOps project that demonstrates application deployment, GitHub integration, CloudWatch monitoring, controlled incident simulation, and the foundation for an AI-assisted DevOps workflow.

---

## 📌 Project Overview

The **Agentic DevOps Lab** is designed to understand how DevOps tools, cloud infrastructure, monitoring, and AI automation can work together.

In this project:

- Application files are managed using Git and GitHub.
- An Ubuntu EC2 instance hosts the application.
- Nginx is used as the web server.
- Amazon CloudWatch monitors EC2 metrics and collects application logs.
- A controlled Nginx failure is simulated to generate error logs.
- The collected logs can be used by a future AI agent for incident analysis and remediation recommendations.

---

## 🏗️ Architecture

```text
                 👨‍💻 Developer
                       |
                       v
                 📦 GitHub
                       |
                       v
              ⚙️ GitHub Actions
                       |
                       v
              ☁️ AWS EC2 (Ubuntu)
                       |
                       v
                 🌐 Nginx
                       |
                       v
                🖥️ Application
                       |
                       v
              📊 Amazon CloudWatch
                /              \
               v                v
         EC2 Metrics       Application Logs
                                  |
                                  v
                           🤖 AI Agent
                                  |
                                  v
                    Analysis → Recommendation
```

> **Note:** The AI-agent stage is the planned extension of this lab. The current project focuses on deployment, monitoring, and incident-log collection.

---

## 🛠️ Technologies Used

| Technology | Purpose |
|---|---|
| AWS EC2 | Host the application |
| Ubuntu | Server operating system |
| Nginx | Web server / reverse proxy |
| Git | Version control |
| GitHub | Source code hosting |
| GitHub Actions | CI/CD automation |
| Amazon CloudWatch | Metrics and log monitoring |
| IAM | Secure AWS permissions |
| Shell Scripting | Server automation |
| AI Agent | Planned incident analysis and remediation |

---

## 📂 Project Structure

```text
Agentic-devops-lab/
│
├── .github/
│   └── workflows/
│       └── deploy.yml
│
├── index.html
├── README.md
│
├── Agentic-DevOps-Lab-Documentation.pdf
├── Agentic-DevOps-Lab-Documentation.docx
└── Agentic-DevOps-Lab-Presentation.pptx
```

> Your actual project files may differ depending on your implementation.

---

## 🚀 Project Implementation

### Step 1: Create the EC2 Instance

- Launch an Ubuntu EC2 instance.
- Use a small instance suitable for a beginner lab.
- Configure the required security group rules.
- Connect to the instance using SSH.

Example:

```bash
ssh -i your-key.pem ubuntu@your-ec2-public-ip
```

---

### Step 2: Update the Ubuntu Server

```bash
sudo apt update
sudo apt upgrade -y
```

---

### Step 3: Install Nginx

```bash
sudo apt install nginx -y
```

Start Nginx:

```bash
sudo systemctl start nginx
```

Enable it at boot:

```bash
sudo systemctl enable nginx
```

Check the service:

```bash
sudo systemctl status nginx
```

---

### Step 4: Deploy the Application

Copy your application files to the Nginx web directory:

```bash
sudo cp index.html /var/www/html/
```

Restart Nginx:

```bash
sudo systemctl restart nginx
```

Test locally:

```bash
curl http://localhost
```

You can also open the EC2 public IP in your browser.

---

## 🔄 GitHub Workflow

The project uses GitHub to store source code and deployment configuration.

### Git Commands

```bash
git status
```

```bash
git add .
```

```bash
git commit -m "Update Agentic DevOps lab"
```

```bash
git push origin main
```

### If Push Shows `fetch first`

This means the remote repository contains changes that are not available locally.

```bash
git pull --rebase origin main
```

Then:

```bash
git push origin main
```

### If Git Reports Divergent Branches

Configure pull behavior:

```bash
git config pull.rebase true
```

Then run:

```bash
git pull --rebase origin main
```

> Resolve any conflicts carefully before pushing.

---

## ☁️ Amazon CloudWatch Monitoring

CloudWatch is used to monitor the EC2 instance and collect application logs.

### Metrics Monitored

- CPUUtilization
- NetworkIn
- NetworkOut
- EC2 status checks

### Console Path

```text
AWS Console
   → CloudWatch
   → Metrics
   → EC2
   → Per-Instance Metrics
   → Select agentic-devops-lab
   → CPUUtilization
```

The CPU utilization graph helps identify whether the EC2 instance is under load.

---

## 📋 CloudWatch Log Group

Create a log group using the AWS Console:

```text
CloudWatch
   → Logs
   → Log groups
   → Create log group
```

Recommended log group:

```text
/agentic-devops/app
```

The application log file used in this lab is:

```text
/var/log/agentic-devops/app.log
```

---

## 📦 CloudWatch Agent Configuration

The CloudWatch Agent sends application logs from EC2 to CloudWatch Logs.

### Install the Agent

```bash
cd /tmp
```

```bash
wget https://amazoncloudwatch-agent.s3.amazonaws.com/ubuntu/amd64/latest/amazon-cloudwatch-agent.deb
```

```bash
sudo dpkg -i -E ./amazon-cloudwatch-agent.deb
```

### Configuration File

```text
/opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json
```

Example configuration:

```json
{
  "agent": {
    "metrics_collection_interval": 60,
    "run_as_user": "root"
  },
  "logs": {
    "logs_collected": {
      "files": {
        "collect_list": [
          {
            "file_path": "/var/log/agentic-devops/app.log",
            "log_group_name": "/agentic-devops/app",
            "log_stream_name": "{instance_id}",
            "retention_in_days": 7
          }
        ]
      }
    }
  }
}
```

---

## 🔐 IAM Permissions

The EC2 instance requires permission to send logs to CloudWatch.

### IAM Role

Create an IAM role for EC2 and attach:

```text
CloudWatchAgentServerPolicy
```

Attach the role to the existing EC2 instance.

### Security Best Practice

- Use IAM roles instead of hard-coded AWS access keys.
- Follow the principle of least privilege.
- Never commit AWS credentials or tokens to GitHub.
- Avoid using AdministratorAccess for this lab.

---

## 🧪 Controlled Incident Simulation

To test monitoring and log collection, a controlled Nginx failure was simulated.

### Stop Nginx

```bash
sudo systemctl stop nginx
```

### Generate Error Logs

```bash
echo "$(date) ERROR nginx service stopped unexpectedly" | sudo tee -a /var/log/agentic-devops/app.log
```

```bash
echo "$(date) ERROR Web service unavailable" | sudo tee -a /var/log/agentic-devops/app.log
```

### Example Log Output

```text
ERROR nginx service stopped unexpectedly
ERROR Web service unavailable
```

### Restore Nginx

```bash
sudo systemctl start nginx
```

Check the service:

```bash
sudo systemctl status nginx
```

Test the web server:

```bash
curl http://localhost
```

---

## 🔍 Verify Logs in CloudWatch

Navigate to:

```text
CloudWatch
   → Logs
   → Log groups
   → /agentic-devops/app
   → Log stream
```

Confirm that the generated error messages are visible.

Example:

```text
INFO Application started successfully
INFO Deployment completed successfully
ERROR Web service unavailable
```

> Log delivery may take a short time. Refresh the log stream after generating a new entry.

---

## 🤖 Agentic DevOps — Future Enhancement

The next stage of this project is to integrate an AI agent that can assist with incident management.

### Proposed Workflow

```text
CloudWatch Logs
      ↓
AI Agent Reads Logs
      ↓
Incident Classification
      ↓
Root Cause Analysis
      ↓
Recommended Fix
      ↓
Human Approval
      ↓
Safe Remediation
```

### Example

**Input:**

```text
ERROR Web service unavailable
```

**AI Analysis:**

```text
Nginx may be stopped or unhealthy.
```

**Recommended Action:**

```bash
sudo systemctl status nginx
```

If approved, the agent could recommend or execute a safe restart workflow.

> Automated actions should require appropriate permissions, safeguards, and human approval during the learning stage.

---

## 💰 Cost-Safety Checklist

This project is designed for beginner-friendly AWS usage.

- Use one small EC2 instance.
- Avoid unnecessary additional EC2 instances.
- Avoid detailed monitoring unless required.
- Set CloudWatch log retention to 7 days.
- Delete unused log groups, alarms, and dashboards.
- Stop or terminate unused EC2 resources.
- Check the AWS Billing and Free Tier dashboards regularly.

> AWS Free Tier eligibility and pricing depend on your account and resource usage. Always verify current pricing in the AWS Console.

---

## 🧹 Cleanup

### Stop CloudWatch Agent

```bash
sudo systemctl stop amazon-cloudwatch-agent
```

### Uninstall CloudWatch Agent

```bash
sudo dpkg -r amazon-cloudwatch-agent
```

### AWS Console Cleanup

- Delete the `/agentic-devops/app` log group if no longer needed.
- Delete unused CloudWatch alarms or dashboards.
- Remove unused IAM roles only when safe.
- Stop or terminate the EC2 instance when the lab is complete.

---

## 📄 Project Documentation

- [Read the Agentic DevOps Lab PDF](./Agentic-DevOps-Lab-Documentation.pdf)
- [Download the Word Documentation](./Agentic-DevOps-Lab-Documentation.docx)
- [View the Project Presentation](./Agentic-DevOps-Lab-Presentation.pptx)

---

## 🎯 Learning Outcomes

By completing this project, I learned:

- How to deploy an application on AWS EC2.
- How to use Git and GitHub for version control.
- How to troubleshoot Git push and merge issues.
- How to configure Nginx on Ubuntu.
- How to monitor EC2 using CloudWatch metrics.
- How to collect application logs using the CloudWatch Agent.
- How IAM roles provide secure AWS permissions.
- How to simulate and investigate service failures.
- How AI agents can support future DevOps incident response.

---

## 👨‍💻 Author

**Aman Maniyar**

BCA Graduate | AWS Cloud & DevOps

- GitHub: [maniyaram744-hue](https://github.com/maniyaram744-hue)
- LinkedIn: [Aman Maniyar](https://www.linkedin.com/in/aman-maniyar/)

---

⭐ If you found this project useful, feel free to explore the repository and connect with me.
