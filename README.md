DevOps Take-Home: Java API on AWS Fargate
Overview

## Project Structure

```text
devops-take-home/

├── Docker                  #Docker folder
    ├──Dockerfile           # Multi-stage build for Java/NewRelic
├── pom.xml                 # Maven project dependencies/config
├── newrelic/               # NR Agent binaries and config
│   ├── newrelic.jar        # The agent library for JVM
│   └── newrelic.yml        # Agent settings and app name
├── src/                    # Java Spring Boot source code
│   └── main/java/...       # Health check API controller logic
├── environments/           # Environment-specific TF configurations
│   ├── dev/                # Development variables and state files
│   ├── staging/            # Staging environment config files
│   └── prod/               # Production environment config files
├── terraform/              # Reusable Infrastructure modules
│   └── modules/
│       ├── vpc/            # Networking: Subnets, NAT, IGW
│       ├── alb/            # Load Balancer and target groups
│       ├── ecs/            # Cluster and Fargate task definitions
│       ├── ecr/            # Private Docker registry for images
│       ├── iam/            # Task and Execution security roles
│       ├── logs/           # CloudWatch Log groups/retention
│       └── autoscaling/    # CPU/Memory based scaling policies
└── .github/workflows/      # CI/CD automation pipelines (YAML)
This repository contains the Infrastructure as Code (Terraform), Containerization (Docker), and CI/CD (GitHub Actions) logic to deploy a Spring Boot Java application to AWS Fargate.

Infrastructure Components
VPC: Custom VPC with Public (ALB) and Private (ECS Tasks) subnets across 2 AZs.

Compute: AWS ECS Cluster using the Fargate launch type for serverless execution.

Networking: Application Load Balancer (ALB) routing traffic to ECS services.

Storage: ECR for Docker image management.

Observability: CloudWatch Logs for persistence and New Relic APM for application performance.

1. Prerequisites
AWS CLI configured with appropriate permissions.

Terraform (v1.0+) installed.

New Relic Account (License Key required).

GitHub Repository with the following secrets configured:

AWS_ACCESS_KEY_ID

AWS_SECRET_ACCESS_KEY

NEW_RELIC_LICENSE_KEY

2. Infrastructure (Terraform)
The infrastructure is modularized and supports multiple environments located in ./environments.

How to Run
Bash

cd environments/dev
terraform init
terraform plan
terraform apply
Key Security Decisions
Least Privilege: IAM roles are split between Task Execution (pulling images/logs) and Task Role (app-specific permissions).

Network Isolation: ECS tasks reside in private subnets. The Security Group only allows ingress from the ALB's Security Group on port 8080.



3. CI/CD Pipeline Flow
The GitHub Actions workflow (.github/workflows/deploy.yml) follows this logic:

PR to Main: Triggers Maven tests and Docker build (no push).

Push to Main:

Builds and tags the image with the Git SHA.

Pushes image to ECR.

Updates the ECS Task Definition and triggers a rolling deployment.

Verifies health via the ALB endpoint.

4. Monitoring & Observability setup
```
New Relic Setup
4.1)Go to ```https://newrelic.com``` click on login
![image alt](https://github.com/tabarak23/MAL_TASK/blob/0104e65ac762381547dbbf99f135a87566b3215b/images/Screenshot%20from%202026-01-28%2010-28-31.png)

4.2)create free account 
![image alt](https://github.com/tabarak23/MAL_TASK/blob/0104e65ac762381547dbbf99f135a87566b3215b/images/Screenshot%20from%202026-01-28%2010-28-48.png)
4.3)Give a name 
![image alt](https://github.com/tabarak23/MAL_TASK/blob/0104e65ac762381547dbbf99f135a87566b3215b/images/Screenshot%20from%202026-01-28%2010-29-13.png)
4.4)Select language and cloud infra
![image alt](https://github.com/tabarak23/MAL_TASK/blob/0104e65ac762381547dbbf99f135a87566b3215b/images/Screenshot%20from%202026-01-28%2010-29-38.png)
4.5)Generate licsence key ====>>> click on your name in the left corner down
![image alt](https://github.com/tabarak23/MAL_TASK/blob/0104e65ac762381547dbbf99f135a87566b3215b/images/Screenshot%20from%202026-01-28%2017-01-15.png)

4.6)Click on your "Api keys"
![image alt](https://github.com/tabarak23/MAL_TASK/blob/0104e65ac762381547dbbf99f135a87566b3215b/images/Screenshot%20from%202026-01-28%2017-01-27.png)

4.7)Select first and Click on "Create a key" at top corner right
![image alt](https://github.com/tabarak23/MAL_TASK/blob/0104e65ac762381547dbbf99f135a87566b3215b/images/Screenshot%20from%202026-01-28%2017-01-44.png)

4.8)Give a name to your license 
![image alt](https://github.com/tabarak23/MAL_TASK/blob/0104e65ac762381547dbbf99f135a87566b3215b/images/Screenshot%20from%202026-01-28%2017-02-06.png)

4.9)Copy license and store in the secrets manager
![image alt](https://github.com/tabarak23/MAL_TASK/blob/0104e65ac762381547dbbf99f135a87566b3215b/images/Screenshot%20from%202026-01-28%2017-02-18.png)

5)Creating Alerts 
5.1)Click on Alerts and then ALert Conditions
![image alt](https://github.com/tabarak23/MAL_TASK/blob/0104e65ac762381547dbbf99f135a87566b3215b/images/Screenshot%20from%202026-01-28%2017-04-20.png)


5.2)Click on "New alert condition"
![image alt](https://github.com/tabarak23/MAL_TASK/blob/0104e65ac762381547dbbf99f135a87566b3215b/images/Screenshot%20from%202026-01-28%2017-04-32.png)


5.3)Select NRQL Write your own query
![image alt](https://github.com/tabarak23/MAL_TASK/blob/0104e65ac762381547dbbf99f135a87566b3215b/images/Screenshot%20from%202026-01-28%2017-04-41.png)


5.4)Write query for cpu utilization,memory and latency etc
![image alt](https://github.com/tabarak23/MAL_TASK/blob/0104e65ac762381547dbbf99f135a87566b3215b/images/Screenshot%20from%202026-01-28%2017-05-30.png)
![image alt](https://github.com/tabarak23/MAL_TASK/blob/0104e65ac762381547dbbf99f135a87566b3215b/images/Screenshot%20from%202026-01-28%2017-05-46.png)

6)```Logging
Logs are streamed to CloudWatch under the group /ecs/java-app.
![image alt](https://github.com/tabarak23/MAL_TASK/blob/0104e65ac762381547dbbf99f135a87566b3215b/images/Screenshot%20from%202026-01-28%2017-06-47.png)
Retention: 7 days (configured in terraform/modules/logs).

7)SUCCESS

7.1)![image alt](https://github.com/tabarak23/MAL_TASK/blob/main/images/Screenshot%20from%202026-01-28%2009-00-14.png?raw=true)

waiting for approval for production
7.2)![image alt](https://github.com/tabarak23/MAL_TASK/blob/0104e65ac762381547dbbf99f135a87566b3215b/images/Screenshot%20from%202026-01-28%2009-01-14.png)
Accesing through ALB dns


7.3)Service runnig smoothly on ecs fargate
![image alt](https://github.com/tabarak23/MAL_TASK/blob/0104e65ac762381547dbbf99f135a87566b3215b/images/Screenshot%20from%202026-01-28%2009-16-07.png)
![image alt](https://github.com/tabarak23/MAL_TASK/blob/0104e65ac762381547dbbf99f135a87566b3215b/images/Screenshot%20from%202026-01-28%2009-16-21.png)
Troubleshooting
Health Checks failing? Ensure the /health endpoint in Restapi.java returns a 200 OK and matches the ALB health check path.
