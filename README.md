
# AWS End-to-End CI/CD Workflow – Python Flask App

This project demonstrates how to set up a **fully automated CI/CD pipeline** for a **Python Flask application** using AWS services like **CodePipeline, CodeBuild, and CodeDeploy**.

---

## Step 0: AWS Services Overview

- **CodeCommit** – Git-based version control  
- **CodeBuild** – Builds and tests code, creates Docker images  
- **CodeDeploy** – Deploys apps to EC2 or containers  
- **CodePipeline** – Connects all stages (Source → Build → Deploy)  
- **CodeArtifact** – Stores artifacts (optional)  
- **KMS** – Encrypts secrets and artifacts  

---

## Step 1: Set Up GitHub Repository

- Create a GitHub repository for your Python app.  
- Example path: `day-14/simple-python-app/app.py`  
- Include files: `appspec.yml`, `buildspec.yml`, and deployment scripts.

---

## Step 2: Create AWS CodePipeline

1. Go to AWS → **CodePipeline → Create Pipeline**
2. **Source**: Connect GitHub repository and branch  
3. **Build**: Choose AWS CodeBuild project  
4. **Deploy**: Select CodeDeploy or Elastic Beanstalk  
5. Review and **Create Pipeline**

---

## Step 3: Configure CodeBuild

**File: buildspec.yml**

```yaml
version: 0.2
env:
  parameter-store:
    DOCKER_REGISTRY_USERNAME: /myapp/docker-credentials/username
    DOCKER_REGISTRY_PASSWORD: /myapp/docker-credentials/password
phases:
  install:
    runtime-versions:
      python: 3.11
    commands:
      - pip install -r requirements.txt
  pre_build:
    commands:
      - echo "$DOCKER_REGISTRY_PASSWORD" | docker login -u "$DOCKER_REGISTRY_USERNAME" --password-stdin
  build:
    commands:
      - docker build -t "$DOCKER_REGISTRY_USERNAME/simple-python-flask-app:latest" .
      - docker push "$DOCKER_REGISTRY_USERNAME/simple-python-flask-app:latest"
  post_build:
    commands:
      - echo "Build completed successfully!"
artifacts:
  files:
    - '**/*'

Key Points:


Uses AWS Parameter Store for secure credentials


Automates Docker build and push


Triggered automatically by GitHub commits


Step 4: Trigger the Pipeline


Commit and push changes to GitHub.


CodePipeline detects changes → runs CodeBuild → builds Docker image → triggers CodeDeploy.



Step 5: Configure CodeDeploy (for EC2 Deployment)


Launch EC2 instance (Amazon Linux 2 / Ubuntu)


Install CodeDeploy agent


Install Docker and allow ports 80/5000


Tag instance: Name=sample-python


Create CodeDeploy application and deployment group


appspec.yml
version: 0.0
os: linux
hooks:
  ApplicationStop:
    - location: scripts/stop_container.sh
  AfterInstall:
    - location: scripts/start_container.sh
  ApplicationStart:
    - location: scripts/health_check.sh


Step 6: Deployment Scripts
scripts/start_container.sh
#!/bin/bash
docker pull <user>/simple-python-flask-app:latest
docker run -d --name simple-python-flask-app -p 80:5000 <user>/simple-python-flask-app:latest

scripts/stop_container.sh
#!/bin/bash
docker stop simple-python-flask-app || true
docker rm simple-python-flask-app || true

scripts/health_check.sh
#!/bin/bash
sleep 5
curl -f http://localhost/ || exit 1


Step 7: Test the Deployment


Go to CodePipeline → Watch Source → Build → Deploy stages


Visit: http://<EC2-public-DNS>/ to access your Flask app



Step 8: Key Takeaways


CI/CD flow: GitHub → CodePipeline → CodeBuild → CodeDeploy → EC2


buildspec.yml = Jenkinsfile equivalent


Parameter Store securely handles secrets


Fully automated containerized deployment



Tech Stack
AWS CodePipeline | AWS CodeBuild | AWS CodeDeploy | EC2 | Docker | Python Flask | Parameter Store

---

## 🧠 GitHub Description (Short)

> A complete **AWS CI/CD pipeline** for a Python Flask app usin<img width="1803" height="709" alt="Screenshot 2025-08-31 165909" src="https://github.com/user-attachments/assets/f1c39dcc-5840-46cf-a6b8-f2b97a7a2c35" />
<img width="1089" height="541" alt="Screenshot 2025-08-31 134256" src="https://github.com/user-attachments/assets/450bc17e-5133-48c0-8a37-f0a6f331a9bd" />
<img width="1746" height="689" alt="Screenshot 2025-08-31 001850" src="https://github.com/user-attachments/assets/641c2d68-c31c-4417-b6b2-0f9dd589030e" />
g **CodePipeline, CodeBuild, and CodeDeploy**.  
> Includes Docker build, Parameter Store integration, and automated EC2 deployment.  


