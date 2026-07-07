
# 🚀 DevOps Spring Boot Docker CI/CD Project

## 📌 Project Overview

This project demonstrates a complete end-to-end DevOps CI/CD workflow using a Spring Boot application.

The application is containerized using Docker and automated through Jenkins Pipeline with code quality analysis, security scanning, and automatic deployment.

The project covers the complete software delivery lifecycle:

Code Commit → Build → Code Quality → Security Scan → Docker Build → Deployment → Health Check


---

# 🏗️ Architecture


Developer
   |
   |
 GitHub Repository
   |
   |
 Jenkins CI/CD Pipeline
   |
   |---- Maven Build
   |
   |---- SonarQube Analysis
   |
   |---- Quality Gate Check
   |
   |---- Docker Image Build
   |
   |---- Trivy Security Scan
   |
   |---- Deploy Docker Container
   |
   |
 Spring Boot Application


---

# 🛠️ Tech Stack

## Application
- Java 21
- Spring Boot 3.x
- Maven

## DevOps Tools
- Git & GitHub
- Jenkins
- Docker
- SonarQube
- Trivy

## Deployment
- Docker Container
- Linux Server (Ubuntu)


---

# 📁 Project Structure


```

springboot-devops-ci-cd-fresh
│
├── app
│   ├── src
│   ├── pom.xml
│   ├── Dockerfile
│   └── target
│
├── Jenkinsfile
│
└── README.md

````


---

# 🚀 Run Application Locally


### 1. Clone Repository

```bash
git clone https://github.com/Pratapkumara/springboot-devops-ci-cd-fresh.git

cd springboot-devops-ci-cd-fresh/app
````

### 2. Build Spring Boot Application

```bash
mvn clean package
```

### 3. Build Docker Image

```bash
docker build -t devops-app:1.0 .
```

### 4. Run Docker Container

```bash
docker run -d \
-p 8080:8080 \
--name devops-app \
devops-app:1.0
```

### Application URL

```
http://localhost:8080
```

Expected Output:

```
DevOps App is Running 🚀
```

---

# 🔄 CI/CD Pipeline Workflow

Every code push triggers Jenkins pipeline:

```
GitHub Push

      ↓

Jenkins Pipeline

      ↓

Maven Build

      ↓

SonarQube Code Analysis

      ↓

Quality Gate Validation

      ↓

Docker Image Build

      ↓

Trivy Security Scan

      ↓

Deploy Container

      ↓

Application Health Check

      ↓

Application Running 🚀

```

---

# 🔍 Pipeline Stages

## 1. Checkout Code

Jenkins automatically pulls the latest source code from GitHub.

## 2. Maven Build

Creates executable Spring Boot JAR file.

```bash
mvn clean package -DskipTests
```

## 3. SonarQube Analysis

Checks:

* Code Quality
* Bugs
* Vulnerabilities
* Code Smells

## 4. Quality Gate

Pipeline continues only when SonarQube Quality Gate passes.

## 5. Docker Build

Creates Docker image:

```
devops-app:1.2
```

## 6. Trivy Security Scan

Scans Docker image for vulnerabilities.

Current Result:

```
Critical Vulnerabilities: 0
```

## 7. Deployment

Old container is removed and new version is deployed automatically.

Container:

```
springboot-app
```

Application Port:

```
8081 → 8080
```

Health Check:

```
/actuator/health
```

Response:

```json
{
 "status":"UP"
}
```

---

# ✅ Completed Features

✔ Spring Boot Application
✔ Docker Containerization
✔ Jenkins CI/CD Pipeline
✔ Automated Maven Build
✔ SonarQube Integration
✔ Quality Gate Validation
✔ Docker Image Creation
✔ Trivy Security Scanning
✔ Automated Deployment
✔ Container Health Check

---

# 📊 Project Status

```
BUILD       ✅ SUCCESS
SONAR       ✅ PASS
SECURITY    ✅ CLEAN
DEPLOYMENT  ✅ SUCCESS
```

---

# 🔮 Future Enhancements

* Docker Hub / AWS ECR Image Push
* Jenkins Webhook Automation
* Nginx Reverse Proxy
* SSL Certificate Setup
* Prometheus Monitoring
* Grafana Dashboard
* Kubernetes Deployment
* Terraform Infrastructure Automation

---

# 👨‍💻 Author

**Pratap Kumar Sahoo**

DevOps CI/CD Practice Project 🚀
