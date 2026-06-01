# 🐍 Two-Tier Flask App — Automated CI/CD Deployment on AWS

![Jenkins](https://img.shields.io/badge/Jenkins-D24939?style=flat&logo=jenkins&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat&logo=docker&logoColor=white)
![Flask](https://img.shields.io/badge/Flask-000000?style=flat&logo=flask&logoColor=white)
![MySQL](https://img.shields.io/badge/MySQL-4479A1?style=flat&logo=mysql&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-232F3E?style=flat&logo=amazon-aws&logoColor=white)
![GitHub](https://img.shields.io/badge/GitHub-181717?style=flat&logo=github&logoColor=white)

A fully automated CI/CD pipeline for deploying a 2-tier Flask + MySQL web application on AWS EC2 using Jenkins, Docker, and Docker Compose. Every code push to GitHub automatically triggers a Jenkins pipeline that builds and deploys the latest version of the application.

---

## 📌 Pipeline Flow

```
Developer pushes code
        │
        ▼
   GitHub Repository
        │
        ▼
   Jenkins Server (AWS EC2)
        │
        ├── Stage 1: Clone repo from GitHub
        ├── Stage 2: Build Docker image (flask-app)
        └── Stage 3: Docker Compose down → up --build
                          │
                          ▼
              ┌─────────────────────┐
              │   Flask Container   │ :5000
              └──────────┬──────────┘
                         │
                         ▼
              ┌─────────────────────┐
              │   MySQL Container   │ :3306
              └─────────────────────┘
```

---

## 🗂️ Repository Structure

```
Two-Tier-Flask-App/
│
├── app.py                  # Flask application
├── requirements.txt        # Python dependencies
├── Dockerfile              # Flask container definition
├── docker-compose.yml      # Multi-container orchestration (Flask + MySQL)
├── Jenkinsfile             # Jenkins pipeline script
└── README.md
```

---

## ⚙️ Pipeline Stages

| Stage | What happens |
|---|---|
| `Clone repo` | Clones source code from GitHub (`branch: main`) |
| `Build image` | Builds Docker image tagged as `flask-app` |
| `Deploy with Docker Compose` | Stops existing containers, rebuilds & restarts with `docker compose up -d --build` |


---

## 🛠️ Tech Stack

| Category | Tools |
|---|---|
| CI/CD | Jenkins |
| Source Control | Git, GitHub |
| Containerisation | Docker, Docker Compose |
| Backend | Python, Flask |
| Database | MySQL |
| Cloud | AWS EC2 (Ubuntu 22.04 LTS) |
| Networking | Docker Networks (two-tier) |

---

