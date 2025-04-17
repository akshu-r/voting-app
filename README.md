# Example Voting App

A simple distributed application running across multiple Docker containers.

## Getting started

# Voting App - DevOps Project 🚀

This is a sample microservices-based **Voting App** deployed using a complete DevOps CI/CD pipeline on a **Kubernetes cluster (EKS)** with **Jenkins**, **Docker**, **Terraform**, and **Prometheus** for monitoring.

---

## Tech Stack

- **Frontend:** Python Flask App (`vote`)
- **Backend Services:**
  - `result`: Node.js App
  - `worker`: .NET App
- **Database:** PostgreSQL & Redis
- **Containerization:** Docker
- **Orchestration:** Kubernetes (AWS EKS)
- **CI/CD:** Jenkins
- **Infrastructure as Code:** Terraform
- **Monitoring:** Prometheus
- **Cloud:** AWS (EKS, EC2, S3, IAM, EBS)

---

## 🛠️ Project Structure

```bash
voting-app/
├── jenkins-pipeline/                  # Jenkins pipeline configs
├── k8s-specifications/                # Kubernetes deployment and service YAMLs
│   ├── vote-deployment.yaml
│   ├── result-deployment.yaml
│   ├── prometheus-alertmanager-pv.yaml
│   └── ...
├── terraform/                         # Terraform files for EKS infrastructure
├── monitoring/                        # Prometheus setup
├── vote/                              # Flask voting app
├── result/                            # Node.js result app
├── worker/                            # .NET worker app
└── README.md

**## Output**

![image](https://github.com/user-attachments/assets/217f949d-8803-41a5-ad65-28cfd8ac970d)
![image](https://github.com/user-attachments/assets/6c1bcb28-e8dd-43ca-83f6-9745db8f4ff6)


