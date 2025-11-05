
## Deploy a 3-Tier application using a secure CI/CD pipeline built with Jenkins, Docker, and Kubernetes on a cloud in a DevSecOps Environment

![devops (2)](https://github.com/user-attachments/assets/a31f9cba-1f10-47f8-bb84-ee5846c5f699)

## Overview

This project demonstrates how to build and deploy a **3-Tier application** (client, API/backend, database) with:

  - 🔄 Automated CI/CD using Jenkins
  - 🛡️ Static code analysis & security scanning (SonarQube, Trivy)
  - 🐳 Containerization with Docker
  - ☸️ Orchestration with Kubernetes
  - 📊 Monitoring & alerting using Prometheus & Grafana

## 🛠️ Deployment Steps

This section details the step-by-step process for deploying the application from local development to a production cloud environment.

### 1. Local Testing
Before setting up the automated workflow, the application was **manually tested locally** to understand its functionality and dependencies.

### Steps Followed

**For client**
```bash
cd client
npm install
npm test
npm start
```
**For API**
```bash
cd ../api
npm install
npm start
```
**Setup MySQL Database**

- Installed MySQL locally.
- Created database crud_app and tables using SQL scripts.
- Configured backend to connect to MySQL.
  
**Run Application**

- Frontend served via Apache locally.
- Backend API verified to connect to the database.

✅ Local testing validated application functionality before CI/CD automation.

<img width="991" height="875" alt="local-testcase" src="https://github.com/user-attachments/assets/7a2dcedc-614d-47b6-b168-d50c12c352ad" />



# 🧰 CI/CD Pipeline



<img width="1855" height="549" alt="cicdnew" src="https://github.com/user-attachments/assets/7edb7d5d-8f70-49c8-944d-77ec2bd24e2e" />

**1. Code Commit (Source Stage)**

- Developers push code to the GitHub repository (main branch).
- Jenkins is configured with a GitHub webhook to trigger the pipeline automatically on every commit.

**2. Checkout Stage**

- Jenkins pulls the latest code from GitHub using the repository URL.
- The pipeline defines a shared workspace for both frontend and backend.

**3. Build & Dependency Installation**

- Node.js and npm are installed via Jenkins tools.
- Dependencies are installed for both frontend (client/) and backend (api/).
```bash
stage('Build') {
 steps {
     dir('client') {
         sh 'npm install'
     }
     dir('api') {
         sh 'npm install'
     }
 }
}
```
**4. Frontend & Backend Client Compile**

- Performs syntax validation for all JavaScript files in the both Frontend(client) and Backend(api) files.
- Using this command
  ```bash
   sh 'find . -name "*.js" -exec node --check {} +'
  ```

**5. Gitleaks Scan**

- Scans both frontend and backend repositories for any hardcoded secrets (API keys, tokens, passwords).

**6. SonarQube Analysis**

- Jenkins triggers SonarQube analysis using sonar-scanner.
- Checks for code smells, bugs, and vulnerabilities.

**7. Code Quality Scan (SonarQube)**

- Quality gates must pass before the pipeline proceeds.
- A SonarQube webhook is configured to communicate back to Jenkins — this allows Jenkins to wait for Sonar analysis results before proceeding to the next stage.

**Webhook Setup (One-time)**

- Open SonarQube → Administration → Configuration → Webhooks.
- Add a new webhook with the Jenkins URL:
- http://<jenkins-server-url>/sonarqube-webhook/
- Jenkins will then automatically receive the scan results and continue only if the Quality Gate passes.

**8. Trivy FS Scan (Filesystem Vulnerability Scan)**

- This stage runs Trivy to perform a filesystem (FS) scan of the source code and project dependencies before building Docker images.

It helps identify:
- Vulnerable packages in the Node.js modules (npm packages)
- Configuration file risks (e.g., .env, package-lock.json)
- Potential exposure of secrets or insecure libraries

**9. Docker Build & Trivy Scan**

- Docker images are built for both client and API.
- Trivy scans images for security vulnerabilities (CVEs).
- Fails pipeline if critical vulnerabilities are found.

**10. Push to Container Registry**

- Authenticates with Docker Hub.
- Pushes both images for deployment.

**11. Manual Approval for Production**

- This stage introduces a manual approval gate to ensure that deployment to the production environment only occurs after explicit human authorization.

**12. Deployment to Kubernetes (EKS)**

- Jenkins connects to the AWS EKS cluster using kubectl.
- Applies all manifests (k8s-prod/) including services, deployments, config maps, etc.
- Ensures zero downtime rollout with kubectl rollout status.


**13. Post-Deployment Validation**

- Jenkins verifies service endpoints and pod health.
- Runs simple smoke tests using curl or custom scripts.

# 🏗️ Terraform Setup

This project uses Terraform to automate the provisioning of cloud infrastructure components on AWS, ensuring reproducible and version-controlled environment creation.

**🌍 Infrastructure Overview**

Terraform provisions:

- VPC with public and private subnets across multiple Availability Zones
- Internet Gateway & NAT Gateway for outbound internet access
- Security Groups for controlled ingress/egress
- EKS Cluster and Node Groups
- IAM Roles and Policies for EKS, Jenkins, and Worker Nodes

**⚙️ Setup Steps**

**1. Install Terraform**

Download and install Terraform:
```bash
sudo apt-get update
sudo apt-get install -y terraform
terraform -version
```
**2. Install AWS CLI**

Install AWS CLI on your machine or Jenkins agent.
Configure AWS credentials

**3. Initialize Terraform**

Navigate to Terraform configuration directory:
```bash
cd infrastructure
terraform init
```

This command initializes the working directory, downloads necessary providers (e.g., AWS), and sets up the backend (if configured).

**4. Review and Validate Configuration**

Before applying, validate the syntax and view the plan:
```bash
terraform fmt
terraform validate
terraform plan 
```
**5. Apply Infrastructure Changes**

Apply the plan to create AWS resources:
```bash
terraform apply -auto-approve
```

# ☸️ Kubernetes Setup

This project uses Kubernetes (EKS) for container orchestration and high availability across multiple nodes and environments.

**🧱 Cluster Architecture**

- Cluster Type: Amazon EKS (Elastic Kubernetes Service)
- Node Groups: 2–3 worker nodes spread across multiple Availability Zones (for fault tolerance)
- Networking: Managed via AWS VPC, Subnets, Internet Gateway, Route Tables, and Security groups
- Load Balancing: AWS Elastic Load Balancer routes traffic to frontend and backend services
- Storage: Persistent Volume Claims (PVCs) provisioned using EBS

**⚙️ Setup Steps**

**1. Create EKS Cluster (Terraform)**

**2. Configure kubectl**

✅ This connects Jenkins agent to the EKS cluster.

**3. Install eksctl**

Simplifies EKS cluster management and add-on deployments.


**4. Create Kubernetes Namespaces**

dev → Development

prod → Production

monitoring → Prometheus/Grafana

**5. Deploy MySQL StatefulSet** 
with Secret, ConfigMaps, Service, and persistent storage (mysql.yaml).

**6. Deploy Backend API** (backend.yaml) with environment variables linked to MySQL Secret/ConfigMap.

**7. Deploy Frontend Client** (frontend.yaml).

**8. Create Ingress with TLS** (ingress.yaml) to route traffic for frontend & backend via NGINX, secured with cert-manager.

**9. Deploy StorageClass** (sc.yaml) to provision EBS volumes dynamically.

**10. Deploy Add-ons:**

- EBS CSI Driver
- NGINX Ingress Controller
- cert-manager

**11. Verify Deployment:**

Check pods and services (kubectl get pods,svc -n prod)

Once the external IP is available, access the app in browser.



# 📊 Monitoring Stack (Prometheus + Grafana)

This section deploys Prometheus and Grafana using Helm on Kubernetes. All services are exposed via LoadBalancer for external access.

**1. Add Prometheus Helm repository**
```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
```

**2. Update repo to fetch latest charts**
```bash
helm repo update
```
**3. Created values.yaml for Custom Configuration**

**4. Deploy Helm Chart**

Deploy or upgrade kube-prometheus-stack in 'monitoring' namespace
```bash
helm upgrade --install monitoring prometheus-community/kube-prometheus-stack -f values.yaml -n monitoring --create-namespace
```
**5. Patch Services for External Access**
```bash
kubectl patch svc monitoring-kube-prometheus-prometheus -n monitoring -p '{"spec": {"type": "LoadBalancer"}}'
kubectl patch svc monitoring-kube-state-metrics -n monitoring -p '{"spec": {"type": "LoadBalancer"}}'
kubectl patch svc monitoring-prometheus-node-exporter -n monitoring -p '{"spec": {"type": "LoadBalancer"}}'
```

<img width="1593" height="807" alt="monitor -ks8" src="https://github.com/user-attachments/assets/ef78a80b-3a81-4c00-90ef-e0c512825147" />

✅ After these commands:

Prometheus and Grafana dashboards are accessible externally

Node Exporter and kube-state-metrics endpoints are exposed for monitoring



<img width="1606" height="703" alt="local-pagenew" src="https://github.com/user-attachments/assets/73fa7d4f-8574-4d7f-94c1-0c755d245b07" />

