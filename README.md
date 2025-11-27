# 📚 MEAN CRUD Application – DevOps Deployment on AWS

This project is a full-stack **CRUD application** built using the **MEAN Stack (MongoDB, Express.js, Angular, Node.js)**.  
It allows users to create, read, update, delete, and search tutorials.

This documentation covers full **DevOps deployment** involving:
- Docker & Docker Compose
- CI/CD using GitHub Actions
- Infrastructure deployment using Terraform
- Deployment on AWS EC2 (Ubuntu)
- Secure MongoDB Network Configuration

---

## 🚀 Application Overview

| Component | Technology |
|----------|------------|
| Frontend | Angular 15, TypeScript |
| Backend | Node.js, Express.js |
| Database | MongoDB |
| Containerization | Docker, Docker Compose |
| CI/CD | GitHub Actions, Docker Hub |
| Infrastructure | Terraform, AWS EC2 (Ubuntu) |
| Reverse Proxy (Optional) | NGINX |

---

## 🛠️ Features

- ➕ Create a Tutorial<br/>
- ✏️ Edit a Tutorial<br/>
- 🗑️ Delete a Tutorial<br/>
- 🔍 Search tutorials by title<br/>
- 📦 Dockerized Frontend & Backend<br/>
- 🔐 Secure internal MongoDB (No public exposure)<br/>

---

## 🐳 Docker Containerization

### 🔹 Dockerfile (Backend)
Located under `backend/`

- Built a Node.js container
- Installed dependencies
- Exposed port `3000`

### 🔹 Dockerfile (Frontend)
Located under `frontend/`

- Node build stage
- NGINX static server stage
- Exposed port `80`

---

## 📦 Production `docker-compose.yml`

```yaml
version: "3.9"

services:
  mongodb:
    image: mongo
    container_name: mongo
    restart: always
    volumes:
      - mongo_data:/data/db

  backend:
    image: <your-dockerhub-username>/backend:latest
    container_name: backend
    restart: always
    ports:
      - "3000:3000"
    environment:
      MONGODB_URI: mongodb://mongo:27017/dd_db
    depends_on:
      - mongodb

  frontend:
    image: <your-dockerhub-username>/frontend:latest
    container_name: frontend
    restart: always
    ports:
      - "8040:80"
    depends_on:
      - backend

volumes:
  mongo_data:
```
🔐 MongoDB is NOT exposed externally (secure)

## 🔄 CI/CD — GitHub Actions Workflow

📌 Located at: .github/workflows/deploy.yml<br/>
Pipeline Tasks<br/>
Trigger on push to main<br/>
Build Frontend & Backend Docker images<br/>
Push images to Docker Hub<br/>

Docker Hub Authentication via Secrets:

| Secret Key           | Description                          |
| -------------------- | ------------------------------------ |
| `DOCKERHUB_USERNAME` | Docker Hub username                  |
| `DOCKERHUB_TOKEN`    | Docker Hub access token (read/write) |

## ☁️ Terraform — AWS EC2 Deployment

Folder structure:<br/>
crud-dd-aws-iac/
  ├── main.tf
  ├── variables.tf
  ├── outputs.tf
  └── terraform.tfvars
  
EC2 Configuration:

Ubuntu 22.04 LTS<br/>

t2.micro<br/>

16GB storage<br/>

Security Group:<br/>

22 (SSH)<br/>

80 (HTTP) (if NGINX enabled)<br/>

8040 (Frontend)<br/>

3000 (Backend)<br/>

❌ MongoDB is not public<br/>

Terraform Commands
```
terraform init
terraform plan
terraform apply
```

## 🐳 Deployment on EC2
1️⃣ SSH into EC2
```
ssh -i <your-key>.pem ubuntu@<public-ip>
```

2️⃣ Install Docker & Docker Compose
```
sudo apt update -y
sudo apt install docker.io -y
sudo systemctl enable docker
sudo systemctl start docker
sudo usermod -aG docker $USER
sudo reboot
```

After reboot, install compose:
```
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

3️⃣ Deploy Container Stack
```
mkdir mean-app && cd mean-app
wget https://raw.githubusercontent.com/<your-username>/<repo>/main/docker-compose.yml
docker-compose pull
docker-compose up -d
docker ps
```

4️⃣ Access Application
```
http://<public-ip>:8040
```

## 🔐 Security Practices Followed

| Security                  | Why                         |
| ------------------------- | --------------------------- |
| MongoDB not exposed       | Prevent hacking & data loss |
| Separate images for FE/BE | Cleaner CI/CD               |
| PAT for Docker Hub        | Protects credentials        |
| Terraform-managed EC2     | No manual VM setup mistakes |

## 🙌 Credits

DevOps Implementation by Hitesh Darshan
Built using MEAN Stack + Docker + AWS Terraform CI/CD
