🚀 Static Website Deployment using Jenkins, Docker & Nginx

This project demonstrates an End-to-End CI/CD Pipeline for deploying a multi-page static website using:

Jenkins (CI/CD automation)

Docker (Containerization)

Docker Hub (Image Registry)

Nginx (Web Server)

GitHub (Source Code Management)

📌 Project Overview

This project automates the following workflow:

Developer pushes code to GitHub

Jenkins pulls the latest code

Docker image is built

Container is tested

Image is pushed to DockerHub

Application is deployed on Nginx server

📂 Project Structure
.
├── Jenkinsfile
├── Dockerfile
├── index.html
├── about.html
├── contact.html
└── css/
      └── style.css
⚙️ Prerequisites

Make sure the following are installed:

Jenkins

Docker

Git

DockerHub Account

Nginx (for deployment server)

🐳 Dockerfile
FROM nginx:latest

RUN rm -rf /usr/share/nginx/html/*

COPY . /usr/share/nginx/html/

EXPOSE 80
🔁 Jenkins Pipeline (Declarative)
pipeline {
    agent any

    environment {
        IMAGE_NAME = "yourdockerhubusername/static-website"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/yourusername/yourrepo.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Test Docker Image') {
            steps {
                sh '''
                docker run -d --name test-container -p 8081:80 $IMAGE_NAME
                sleep 10
                docker ps
                docker stop test-container
                docker rm test-container
                '''
            }
        }

        stage('Push to DockerHub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-creds') {
                        docker.image(IMAGE_NAME).push('latest')
                    }
                }
            }
        }

        stage('Deploy to Server') {
            steps {
                sh '''
                docker pull $IMAGE_NAME:latest
                docker stop production-container || true
                docker rm production-container || true
                docker run -d --name production-container -p 80:80 $IMAGE_NAME:latest
                '''
            }
        }
    }

    post {
        success {
            echo 'Deployment Successful!'
        }
        failure {
            echo 'Deployment Failed!'
        }
    }
}
🔐 Jenkins Credentials Setup

To push images to DockerHub:

Go to Manage Jenkins → Manage Credentials

Add new credentials:

Kind: Username with password

Username: DockerHub username

Password: DockerHub Access Token (Read & Write)

ID: dockerhub-creds

🔄 CI/CD Workflow Explained
1️⃣ Code Push

Developer pushes updated HTML/CSS files to GitHub.

2️⃣ Jenkins Trigger

Pipeline starts automatically or manually.

3️⃣ Build Stage

Docker builds an image using Nginx base image.

4️⃣ Test Stage

Container runs on port 8081 to verify application works.

5️⃣ Push Stage

Image is pushed to DockerHub registry.

6️⃣ Deployment Stage

Latest image is pulled and deployed on production server (Port 80).

🌐 Access Application

After successful deployment:

http://<server-ip>
🧠 Key Learning Outcomes

Declarative Jenkins Pipeline

Docker Image creation

Docker container lifecycle

DockerHub integration

CI/CD automation

Production-style deployment workflow

📊 Architecture Diagram (Logical Flow)
Developer → GitHub → Jenkins Pipeline → Docker Build → DockerHub → Production Server (Nginx)
🏆 What This Project Demonstrates

✅ Real-world CI/CD implementation
✅ Dockerized static website
✅ Automated build, test, push & deploy
✅ Industry-standard DevOps workflow



<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/4db8944a-8624-4312-a2a3-fff3078260d2" />

