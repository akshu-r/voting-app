# 🗳️ Voting App — Microservices Demo with Docker, Kubernetes, and CI/CD

This is a microservices-based **Voting Application** that demonstrates how to build, containerize, and deploy a full-stack app using **Docker**, **Kubernetes**, and integrate with **CI/CD tools** like Jenkins.

---

## 📦 Tech Stack

| Layer        | Technology              |
|-------------|--------------------------|
| Frontend     | Python Flask             |
| Backend      | Node.js + Express        |
| Database     | PostgreSQL               |
| Messaging    | Redis                    |
| Result App   | .NET Core                |
| Queue Worker | Python                   |
| Orchestration | Docker, Kubernetes      |

---

## 🧱 Architecture Diagram

![Architecture](./architecture-diagram.png) <!-- Replace with actual path or upload -->

### 🔍 Description

1. **Vote App** — Frontend written in Python/Flask allowing users to vote between two options.
2. **Redis** — Stores the votes temporarily as an in-memory datastore.
3. **Worker** — A Python service that pulls data from Redis and pushes it to PostgreSQL.
4. **PostgreSQL** — Stores the permanent result of the voting.
5. **Result App** — A .NET Core app displays the voting results.
6. **Docker Compose / Kubernetes** — Used to orchestrate multi-container setup.

---

## 🚀 Quick Start with Docker Compose

### ✅ Prerequisites

- Docker
- Docker Compose

### ▶️ Run with Docker Compose

```bash
git clone https://github.com/akshu-r/voting-app.git
cd voting-app
docker-compose up --build


**## Output**

![image](https://github.com/user-attachments/assets/217f949d-8803-41a5-ad65-28cfd8ac970d)
![image](https://github.com/user-attachments/assets/6c1bcb28-e8dd-43ca-83f6-9745db8f4ff6)


