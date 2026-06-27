# ECSAutomation

CI/CD pipeline that builds a Java Spring Boot application, packages it into a Docker image, pushes to AWS ECR, and deploys to ECS Fargate using CloudFormation — all automated through Jenkins.

---

## Architecture

```
GitHub Push
    |
    v
Jenkins Pipeline (Jenkinsfile)
    |
    +-- mvn clean package       (build JAR)
    +-- docker build            (create image)
    +-- docker push -> ECR      (push to registry)
    +-- aws cloudformation      (deploy stack to ECS Fargate)
    |
    v
ECS Fargate Service
    |
    v
Application Load Balancer -> Spring Boot App
```

---

## Stack

| Layer | Technology |
|---|---|
| Application | Java, Spring Boot |
| CI/CD | Jenkins, Jenkinsfile |
| Containerisation | Docker |
| Registry | AWS ECR |
| Orchestration | AWS ECS Fargate |
| Infrastructure | AWS CloudFormation |
| Load Balancing | AWS ALB |

---

## Prerequisites

- Jenkins with Docker and AWS CLI installed
- AWS credentials configured (ECR push + CloudFormation deploy permissions)
- Java 11+, Maven

---

## Setup

1. Configure Jenkins credentials for AWS
2. Create ECR repository and update `ECR_REPO` in Jenkinsfile
3. Trigger the pipeline — it handles build, push, and deploy automatically

---

## Files

| File | Purpose |
|---|---|
| `Jenkinsfile` | Full CI/CD pipeline definition |
| `Dockerfile` | Containerises the Spring Boot JAR |
| `cloudformation/` | ECS Fargate stack + ALB definition |
| `src/` | Spring Boot application source |

