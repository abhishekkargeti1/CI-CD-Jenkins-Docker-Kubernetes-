# 🚀 CI/CD Pipeline with Jenkins, Docker & Kubernetes

A complete **CI/CD implementation for a Java Spring Boot application** using **Jenkins, Maven, Docker, Docker Hub, and Kubernetes**.

This project demonstrates how application source code can be automatically built, containerized, pushed to a Docker registry, deployed to a Kubernetes cluster, and verified using a Jenkins pipeline.

---

## 📌 Project Overview

The goal of this project is to implement an automated deployment workflow for a Spring Boot authentication service.

The CI/CD pipeline follows this flow:

```text
Developer
    │
    ▼
 GitHub
    │
    ▼
 Jenkins
    │
    ├── Clone Source Code
    │
    ├── Build Spring Boot JAR
    │
    ├── Build Docker Image
    │
    ├── Test
    │
    ├── Push Image to Docker Hub
    │
    ├── Deploy to Kubernetes
    │
    └── Verify Deployment
    │
    ▼
 Kubernetes Cluster
    │
    ├── Spring Boot Pods
    │
    └── Kubernetes Service
```

---

## 🛠️ Technologies Used

| Technology       | Purpose                           |
| ---------------- | --------------------------------- |
| Java 17          | Application development           |
| Spring Boot      | Backend application               |
| Maven            | Build and package the application |
| Jenkins          | CI/CD automation                  |
| Docker           | Application containerization      |
| Docker Hub       | Docker image registry             |
| Kubernetes       | Container orchestration           |
| kubectl          | Kubernetes cluster management     |
| MySQL            | Application database              |
| GitHub           | Source code management            |
| Ubuntu / AWS EC2 | Infrastructure                    |

The project uses Java 17 and Spring Boot 4.0.5 according to the Maven configuration.

---

# 🔄 CI/CD Pipeline

The Jenkins pipeline is divided into multiple stages.

## 1. Code Cloning

Jenkins clones the application source code from the GitHub `main` branch.

```groovy
stage("Code Cloning"){
    steps{
        git url :"https://github.com/abhishekkargeti1/CI-CD-Jenkins-Docker-Kubernetes-.git",
            branch:"main"
    }
}
```

---

## 2. Build Spring Boot Application

Maven is used to compile the application and generate the executable JAR file.

```bash
mvn clean package -DskipTests
```

The generated JAR is then used by the Docker build process.

---

## 3. Build Docker Image

Jenkins builds a Docker image using the project's `Dockerfile`.

```bash
docker build -t abhishekkargeti/myauthserviceimage:latest .
```

The Dockerfile uses the Eclipse Temurin Java 17 JRE image and runs the Spring Boot JAR using:

```dockerfile
FROM eclipse-temurin:17-jre

WORKDIR /app

COPY target/Auth-Application-0.0.1-SNAPSHOT.jar app.jar

ENTRYPOINT ["java","-jar","app.jar"]
```

---

## 4. Push Docker Image to Docker Hub

Jenkins securely retrieves Docker Hub credentials from Jenkins Credentials Manager.

The pipeline performs:

```bash
docker login
docker image tag
docker push
```

The image is pushed to:

```text
abhishekkargeti/authserviceimages:latest
```

The Docker credentials are referenced through Jenkins credentials using the credential ID:

```text
DockerCred
```

No Docker Hub password should be stored directly inside the Jenkinsfile.

---

# ☸️ Kubernetes Deployment

After the Docker image is pushed, Jenkins deploys the application to Kubernetes.

The deployment stage executes:

```bash
kubectl apply -f auth-server-deployment.yml
```

The application is deployed into the Kubernetes namespace:

```text
authserver
```

The Jenkins pipeline uses the following Kubernetes resources:

```text
Namespace
   │
   ├── Deployment
   │      │
   │      └── Spring Boot Pods
   │
   └── Service
```

The deployment name configured in the pipeline is:

```text
auth-server-deployment
```

The application container name is:

```text
auth-server-app
```

---

# 🔍 Kubernetes Deployment Verification

After deployment, Jenkins verifies the Kubernetes environment using:

```bash
kubectl get nodes

kubectl get pods -n authserver -o wide

kubectl get svc -n authserver

kubectl get deployment -n authserver
```

This allows the pipeline to verify:

* Kubernetes nodes
* Running application pods
* Kubernetes services
* Deployment status

The verification commands are part of the Jenkins pipeline.

---

# 🐳 Docker Compose

The repository also contains a Docker Compose configuration for running the application with MySQL.

The Compose setup contains:

```text
MySQL
   │
   │
   ▼
Spring Boot Auth Service
```

The Spring Boot container connects to MySQL using the Docker Compose service name:

```text
jdbc:mysql://db:3306/employee
```

The application exposes port:

```text
8080
```

MySQL is mapped to:

```text
3307:3306
```

---

# 📂 Project Structure

```text
CI-CD-Jenkins-Docker-Kubernetes-
│
├── .metadata/
│
├── .mvn/
│   └── wrapper/
│
├── src/
│   └── main/
│       └── java/
│
├── Dockerfile
│
├── Jenkinsfile
│
├── pom.xml
│
├── mvnw
│
├── mvnw.cmd
│
├── auth_service_docker_compose_file.yaml
│
├── Auth-Application-0.0.1-SNAPSHOT.jar
│
├── .gitignore
│
├── .gitattributes
│
└── README.md
```

---

# ⚙️ Prerequisites

Before running the project, install/configure:

### Java

Java 17 or compatible JDK/JRE.

Verify:

```bash
java -version
```

### Maven

Verify:

```bash
mvn -version
```

### Docker

Verify:

```bash
docker --version
```

### Jenkins

Jenkins should have access to:

```text
Git
Maven
Docker
kubectl
Kubernetes cluster
```

### Kubernetes

Verify:

```bash
kubectl version
```

Check cluster connectivity:

```bash
kubectl get nodes
```

---

# 🔐 Jenkins Configuration

Create a Jenkins credential for Docker Hub.

Go to:

```text
Jenkins
   → Manage Jenkins
   → Credentials
   → Global credentials
   → Add Credentials
```

Select:

```text
Kind: Username with password
```

Use:

```text
ID: DockerCred
Username: <Docker Hub username>
Password: <Docker Hub password/token>
```

The Jenkinsfile references this credential using:

```groovy
credentialsId: 'DockerCred'
```

---

# 🚀 Running the Pipeline

Create a Jenkins Pipeline job.

Select:

```text
Pipeline
```

Configure Jenkins to use the repository's:

```text
Jenkinsfile
```

Then run:

```text
Build Now
```

The pipeline executes:

```text
Code Cloning
      ↓
Building JAR
      ↓
Building Docker Image
      ↓
Testing
      ↓
Pushing Docker Image
      ↓
Kubernetes Deployment
      ↓
Verify Deployment
```

---

# 🧪 Verify the Application

After the Jenkins pipeline completes successfully:

### Check Kubernetes nodes

```bash
kubectl get nodes
```

### Check application pods

```bash
kubectl get pods -n authserver -o wide
```

### Check services

```bash
kubectl get svc -n authserver
```

### Check deployment

```bash
kubectl get deployment -n authserver
```

### Check detailed deployment information

```bash
kubectl describe deployment auth-server-deployment -n authserver
```

### Check pod logs

```bash
kubectl logs -n authserver <pod-name>
```

---

# 🔁 Continuous Delivery Workflow

Once the pipeline is configured, the deployment process becomes:

```text
Code Change
    │
    ▼
GitHub
    │
    ▼
Jenkins
    │
    ▼
Maven Build
    │
    ▼
Spring Boot JAR
    │
    ▼
Docker Build
    │
    ▼
Docker Image
    │
    ▼
Docker Hub
    │
    ▼
Kubernetes
    │
    ▼
Spring Boot Application
```

This reduces manual deployment work and provides a repeatable deployment process.

---

# 🎯 Key Learning Outcomes

Through this project, I gained practical experience with:

* CI/CD pipeline design
* Jenkins Declarative Pipelines
* Maven-based Java builds
* Docker image creation
* Docker Hub image publishing
* Kubernetes deployments
* Kubernetes Services
* `kubectl` commands
* Jenkins Credentials
* Linux/Ubuntu server administration
* Containerized Spring Boot applications
* Application deployment on Kubernetes
* CI/CD troubleshooting and debugging

---

# 🔧 Future Improvements

Possible improvements for this project include:

* Add GitHub Webhook for automatic Jenkins builds
* Add automated unit and integration tests
* Use versioned Docker image tags instead of only `latest`
* Add Kubernetes rolling update strategy
* Add `kubectl rollout status` to the deployment stage
* Add Kubernetes health checks
* Add ConfigMaps and Secrets
* Move database credentials to Kubernetes Secrets
* Add SonarQube for code quality analysis
* Add monitoring with Prometheus and Grafana
* Add centralized logging
* Implement Kubernetes Ingress
* Add HTTPS/TLS
* Use separate environments for Development, Staging and Production

---

# ⚠️ Security Note

Do **not** commit real passwords, API keys, database credentials, Docker Hub tokens, AWS credentials, or other secrets to GitHub.

Use:

```text
Jenkins Credentials
Kubernetes Secrets
Environment Variables
AWS Secrets Manager
```

instead.

Before using this repository in a real environment, review the Docker Compose configuration and replace any hard-coded credentials with environment variables or secrets.

---

# 📚 Repository

GitHub:

https://github.com/abhishekkargeti1/CI-CD-Jenkins-Docker-Kubernetes-

---

# 👨‍💻 Author

**Abhishek Kargeti**

Java Developer | Spring Boot | DevOps | Docker | Kubernetes | Jenkins

---

## ⭐ If you found this project useful

Feel free to ⭐ the repository and explore the implementation.
