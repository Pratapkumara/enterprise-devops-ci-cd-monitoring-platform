# 🚀 End-to-End DevOps CI/CD Pipeline for Spring Boot Application

![Java](https://img.shields.io/badge/Java-21-orange)
![Spring Boot](https://img.shields.io/badge/SpringBoot-3.x-brightgreen)
![Maven](https://img.shields.io/badge/Maven-Build-red)
![Jenkins](https://img.shields.io/badge/Jenkins-CI%2FCD-blue)
![Docker](https://img.shields.io/badge/Docker-Container-2496ED)
![SonarQube](https://img.shields.io/badge/SonarQube-Code%20Quality-green)
![Trivy](https://img.shields.io/badge/Trivy-Security-blueviolet)
![Prometheus](https://img.shields.io/badge/Prometheus-Monitoring-orange)
![Grafana](https://img.shields.io/badge/Grafana-Dashboard-F46800)
![AWS](https://img.shields.io/badge/AWS-EC2-yellow)

---

# 📌 Project Overview

This project demonstrates a complete **DevOps CI/CD pipeline** for a Spring Boot application using Jenkins, Docker, SonarQube, Trivy, Prometheus, Grafana, and AWS EC2.

The pipeline automatically builds the application, performs static code analysis, checks the quality gate, creates a Docker image, pushes it to Docker Hub, scans it for vulnerabilities, and deploys the latest version automatically.

---

# 🏗️ Architecture

```
Developer
      │
      ▼
 GitHub Repository
      │
      ▼
 Jenkins Pipeline
      │
      ├───────────────► Maven Build
      │
      ├───────────────► SonarQube Analysis
      │
      ├───────────────► Quality Gate
      │
      ├───────────────► Docker Build
      │
      ├───────────────► Docker Hub Push
      │
      ├───────────────► Trivy Security Scan
      │
      ▼
 Deploy Spring Boot Container
      │
      ▼
 Monitoring
      ├──► Prometheus
      ├──► Grafana
      └──► Alertmanager
```

---

# 🛠️ Tech Stack

- Java 21
- Spring Boot
- Maven
- Git & GitHub
- Jenkins
- SonarQube
- Docker
- Docker Hub
- Trivy
- Prometheus
- Grafana
- Alertmanager
- Nginx
- AWS EC2
- Linux (Ubuntu)

---

# 📂 Project Structure

```
springboot-devops-ci-cd-fresh/
│
├── app/
│   ├── src/
│   ├── Dockerfile
│   ├── pom.xml
│   └── Jenkinsfile
│
├── docker/
│   ├── docker-compose.yml
│   ├── prometheus.yml
│   └── alertmanager.yml
│
└── README.md
```

---

# ⚙️ CI/CD Pipeline Stages

## 1️⃣ Checkout Source Code

- Pull latest code from GitHub

---

## 2️⃣ Maven Build

```
mvn clean package
```

Builds the Spring Boot application.

---

## 3️⃣ SonarQube Analysis

- Static Code Analysis
- Bug Detection
- Code Smells
- Security Hotspots
- Maintainability

---

## 4️⃣ Quality Gate

Pipeline continues only if the Quality Gate passes.

---

## 5️⃣ Docker Build

```
docker build -t pratapkumar1/devops-app:1.2 .
```

Creates Docker image.

---

## 6️⃣ Docker Push

Push image to Docker Hub.

```
docker push pratapkumar1/devops-app:1.2
```

---

## 7️⃣ Trivy Security Scan

Scans Docker image for

- HIGH vulnerabilities
- CRITICAL vulnerabilities

---

## 8️⃣ Deployment

Automatically deploys latest image

```
docker run -d \
--name devops-app-container \
-p 8082:8080 \
pratapkumar1/devops-app:1.2
```

---

## 9️⃣ Monitoring

Application metrics are collected by

- Prometheus

Visualized using

- Grafana

Alerts handled by

- Alertmanager

---

# 🚀 Pipeline Workflow

```
GitHub Push
      │
      ▼
 Jenkins
      │
      ▼
 Maven Build
      │
      ▼
 SonarQube Scan
      │
      ▼
 Quality Gate
      │
      ▼
 Docker Build
      │
      ▼
 Docker Hub Push
      │
      ▼
 Trivy Scan
      │
      ▼
 Deploy
      │
      ▼
 Monitoring
```

---

# 📊 Monitoring Stack

| Tool | Purpose |
|-------|----------|
| Prometheus | Metrics Collection |
| Grafana | Dashboard |
| Alertmanager | Alerts |

---

# 🔒 Security

The pipeline performs:

- Static Code Analysis
- Docker Image Scanning
- Vulnerability Detection
- Quality Gate Validation

---

# 📸 Screenshots

Add screenshots of:

- Jenkins Dashboard
- 
- Successful Pipeline
- 
- SonarQube Dashboard
- <img width="959" height="479" alt="image" src="https://github.com/user-attachments/assets/35b85f48-5933-4d69-b6b5-690e674f82b9" />

- Docker Hub Repository
- <img width="959" height="421" alt="image" src="https://github.com/user-attachments/assets/3e2a2b7f-5478-4064-88d7-dcd35d830373" />

- Grafana Dashboard
- 
- Prometheus Targets
- 
- Running Application

---

# 📈 Features

- End-to-End CI/CD
- Automated Build
- Automated Testing Ready
- Code Quality Analysis
- Docker Image Creation
- Docker Hub Integration
- Security Scanning
- Automated Deployment
- Monitoring
- Alerting

---

# 🎯 Future Enhancements

- Kubernetes Deployment
- Helm Charts
- ArgoCD GitOps
- Terraform
- AWS EKS
- GitHub Actions
- Nexus Repository
- Blue-Green Deployment
- JaCoCo Code Coverage
- OWASP Dependency Check

---

# 👨‍💻 Author

**Pratap Kumar Sahoo**

GitHub:
https://github.com/Pratapkumara

Docker Hub:
https://hub.docker.com/u/pratapkumar1

---

# ⭐ If you like this project

Give this repository a ⭐ on GitHub.

---
