**Project Title: Jenkins CI/CD Pipeline to Build, Test, and Deploy a Flask App using Docker on WSL Machine**

---

## 🔍 Project Overview:

* **Goal**: Automate clone -> build (Docker) -> test (Python) -> deploy (Docker container) of a Flask app using Jenkins.
* **Machines Involved**:

  * **Jenkins Master**: Ubuntu VM with Jenkins installed (Named: `ubuntuser`)
  * **Jenkins Agent/Slave**: Separate Ubuntu VM configured as Jenkins node (Named: `jenkinsslave`)
  * **Deployment Target**: WSL Ubuntu running on Windows host (IP: 172.26.240.11)

---

## ♻️ Jenkins Setup Summary:

### A. Jenkins Master Setup:

1. Installed Jenkins on Ubuntu machine.
2. Installed suggested plugins during setup.
3. Created SSH credential for GitHub and agent communication.

### B. Jenkins Agent Setup:

1. Created new node in Jenkins UI:

   * **Name**: `jenkinsslave`
   * **Remote root dir**: `/home/jenkins`
   * **Launch method**: Launch via SSH
   * **Credentials**: `jenkins-agent-key` (username: `jenkins`, private key used)

### C. Jenkins Pipeline Project:

1. Freestyle or Pipeline project created in Jenkins UI.
2. Under "Pipeline script" section, added the pipeline code (see below).

---

## 📄 Jenkinsfile Script Used:

```groovy
pipeline {
    agent { label 'jenkinsslave' }

    environment {
        DEPLOY_IP = '172.26.240.11'
    }

    stages {
        stage('Clone') {
            steps {
                git url: 'https://github.com/amreshsharma199/flaskapp.git', branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker Image...'
                sh 'docker build -t flask-jenkins-app .'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'python3 test_app.py'
            }
        }

        stage('Deploy to WSL') {
            steps {
                echo "Deploying to WSL..."
                sh '''
                    docker save flask-jenkins-app -o flaskapp.tar
                    scp flaskapp.tar amresh@${DEPLOY_IP}:/home/amresh/
                    ssh -o StrictHostKeyChecking=no amresh@${DEPLOY_IP} 'docker load -i flaskapp.tar'
                    ssh -o StrictHostKeyChecking=no amresh@${DEPLOY_IP} 'docker rm -f flaskapp || true'
                    ssh -o StrictHostKeyChecking=no amresh@${DEPLOY_IP} 'docker run -d --name flaskapp -p 5000:5000 flask-jenkins-app'
                '''
            }
        }
    }
}
```

---

## 🤔 Commands Explanation:

### Build Stage:

* `docker build -t flask-jenkins-app .`

  * Creates a Docker image named `flask-jenkins-app` from the current directory (which must contain a valid `Dockerfile`).

### Test Stage:

* `python3 test_app.py`

  * Runs a test script (you must have this file in your repo).

### Deploy Stage:

```bash
1. docker save flask-jenkins-app -o flaskapp.tar
   # Saves Docker image as tar file

2. scp flaskapp.tar amresh@172.26.240.11:/home/amresh/
   # Copies image to remote WSL machine

3. ssh amresh@172.26.240.11 'docker load -i flaskapp.tar'
   # Loads image on WSL machine

4. ssh amresh@172.26.240.11 'docker rm -f flaskapp || true'
   # Stops & removes old container (if exists)

5. ssh amresh@172.26.240.11 'docker run -d --name flaskapp -p 5000:5000 flask-jenkins-app'
   # Starts container from newly built image
```

---

## 📖 Jenkins UI Navigation Summary (for beginners):

1. **Create Node (Agent)**:

   * Jenkins Dashboard > Manage Jenkins > Manage Nodes > New Node > Permanent Agent
   * Add name (`jenkinsslave`), set remote dir, launch via SSH, select SSH key.

2. **Create Pipeline Project**:

   * Dashboard > New Item > Enter name > Select Pipeline > OK
   * Scroll to Pipeline section, paste above `Jenkinsfile` in Script box.

3. **Run the Job**:

   * Click **Build Now**
   * Watch Console Output

---

## ❓ Notes:

* Make sure **Docker is installed on all machines** (including WSL).
* Jenkins Agent VM should have:

  * Docker
  * Python (for test)
* Jenkins Master doesn’t run the build — Agent does all work.
* Your GitHub repo must contain:

  * Flask app files
  * `Dockerfile`
  * `test_app.py`

---

## ✅ Project Verified Status:

* Pipeline successfully builds Docker image on agent
* Test script runs
* Deployment to WSL happens over SSH
* App is accessible on WSL host via `http://localhost:5000`

---

## 📆 Author:

Amresh Sharma Date: July 2025
