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
├── diagrams/               # Architecture & workflow diagrams
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

## 🚀 Getting Started

### Prerequisites

- AWS EC2 instance (Ubuntu 22.04 LTS, t2.micro)
- Jenkins installed and running on port `8080`
- Docker and Docker Compose installed
- Jenkins added to Docker group

### Step 1 — Launch EC2 & Configure Security Group

Open the following inbound ports:

| Port | Purpose |
|---|---|
| 22 | SSH access |
| 8080 | Jenkins dashboard |
| 5000 | Flask application |
| 3306 | MySQL (optional) |

### Step 2 — Install Dependencies on EC2

```bash
# Update packages
sudo apt update && sudo apt upgrade -y

# Install Git, Docker, Docker Compose
sudo apt install git docker.io docker-compose-v2 -y

# Start and enable Docker
sudo systemctl start docker
sudo systemctl enable docker

# Add user to Docker group
sudo usermod -aG docker $USER
newgrp docker
```

### Step 3 — Install Jenkins

```bash
# Install Java
sudo apt install openjdk-17-jdk -y

# Add Jenkins repo & install
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt update && sudo apt install jenkins -y

# Start Jenkins
sudo systemctl start jenkins
sudo systemctl enable jenkins

# Grant Jenkins Docker permissions
sudo usermod -aG docker jenkins
sudo systemctl restart jenkins
```

### Step 4 — Configure Jenkins Pipeline

- Access Jenkins at `http://<ec2-public-ip>:8080`
- Get initial password: `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`
- Create new Pipeline job → Pipeline script from SCM → Git
- Enter repo URL → Script path: `Jenkinsfile`
- Save & click **Build Now**

### Step 5 — Verify Deployment

```bash
# Check running containers
docker ps
```

Access the app at: `http://<ec2-public-ip>:5000`

---

## 🐳 Docker Configuration

### Dockerfile
```dockerfile
FROM python:3.9-slim
WORKDIR /app
RUN apt-get update && apt-get install -y gcc default-libmysqlclient-dev pkg-config && \
    rm -rf /var/lib/apt/lists/*
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 5000
CMD ["python", "app.py"]
```

### docker-compose.yml
```yaml
version: "3.8"
services:
  mysql:
    container_name: mysql
    image: mysql
    environment:
      MYSQL_DATABASE: "devops"
      MYSQL_ROOT_PASSWORD: "root"
    ports:
      - "3306:3306"
    volumes:
      - mysql-data:/var/lib/mysql
    networks:
      - two-tier
    restart: always

  flask:
    build:
      context: .
    container_name: two-tier-app
    ports:
      - "5000:5000"
    environment:
      - MYSQL_HOST=mysql
      - MYSQL_USER=root
      - MYSQL_PASSWORD=root
      - MYSQL_DB=devops
    networks:
      - two-tier
    depends_on:
      - mysql
    restart: always

volumes:
  mysql-data:

networks:
  two-tier:
```

---

## 🔐 Security Practices

- EC2 Security Groups restrict access to only required ports
- Jenkins credentials store manages sensitive data — nothing hardcoded
- Docker group permissions limit Docker access to authorised users only
- MySQL runs in an isolated Docker network — not exposed to public internet

---

## 👨‍💻 Author

**Harsh Nitin Chaudhari** — DevOps Engineer

[![LinkedIn](https://img.shields.io/badge/LinkedIn-0077B5?style=flat&logo=linkedin&logoColor=white)](https://linkedin.com/in/chaudhariharsh)
[![GitHub](https://img.shields.io/badge/GitHub-181717?style=flat&logo=github&logoColor=white)](https://github.com/harshtechnova)
[![Email](https://img.shields.io/badge/Email-D14836?style=flat&logo=gmail&logoColor=white)](mailto:harsh.nitin.chaudhari@outlook.com)

---

## 📄 License

This project is open source and available under the [MIT License](LICENSE).
