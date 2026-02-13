# Data Center Application Deployment on Google Cloud Platform

This repository documents the deployment process of a web-based Data Center application on Google Cloud Platform (GCP). The system is deployed using a cloud-based architecture that separates the application layer and the database layer to improve reliability and scalability.

## Live Deployment

Frontend (Production Domain)
https://ds-datacenter.my.id/

Backend (Public IP Access)
http://34.101.201.82:1337/dashboard

## Architecture Overview

User
  ->
HTTPS Domain
  ->
NGINX Reverse Proxy
  ->
Backend (Node.js - Port 1337)
  ->
Cloud SQL (MySQL 8.0)

The architecture separates:
- Application Layer (Compute Engine VM)
- Database Layer (Cloud SQL)

## Infrastructure Specification

Machine Type : e2-small  
vCPU         : 2  
Memory       : 2 GB  
OS           : Ubuntu 22.04 LTS  
Region       : us-west1  
External IP  : Static  

## 1. VM Provisioning

Create VM using Google Cloud CLI:

gcloud compute instances create datacenter-vm \
  --machine-type=e2-small \
  --zone=us-west1-b \
  --image-family=ubuntu-2204-lts \
  --image-project=ubuntu-os-cloud

Open required firewall rules:

gcloud compute firewall-rules create allow-http \
  --allow tcp:80

gcloud compute firewall-rules create allow-https \
  --allow tcp:443

gcloud compute firewall-rules create allow-node \
  --allow tcp:1337

## 2. Cloud SQL Configuration

Steps to configure Cloud SQL:

1. Create new Cloud SQL instance.
2. Select MySQL 8.0.
3. Choose same region as VM.
4. Enable Public IP.
5. Add VM external IP to Authorized Networks.
6. Create application database.
7. Create database user.

Configure environment variables inside backend:

DB_HOST=<cloud-sql-ip>
DB_USER=<db-user>
DB_PASSWORD=<db-password>
DB_NAME=<db-name>

## 3. Backend Deployment

Connect to VM:

gcloud compute ssh datacenter-vm --zone=us-west1-b

Clone repository:

git clone https://github.com/your-backend-repo.git
cd backend-project
npm install

Run using PM2:

pm2 start ecosystem.config.js
pm2 save
pm2 startup

Backend runs on port 1337.

## 4. Frontend Deployment

Clone frontend repository via SSH:

git clone https://github.com/your-frontend-repo.git
cd frontend-project
npm install
npm run build

## 5. NGINX Reverse Proxy Configuration

Install NGINX:

sudo apt install nginx

Example configuration:

server {
    listen 80;
    server_name ds-datacenter.my.id;

    location / {
        proxy_pass http://localhost:3000;
    }

    location /api {
        proxy_pass http://localhost:1337;
    }
}

Restart NGINX:

sudo systemctl restart nginx

## 6. Monitoring and Reliability

Install Google Cloud Ops Agent:

curl -sSO https://dl.google.com/cloudagents/add-google-cloud-ops-agent-repo.sh
sudo bash add-google-cloud-ops-agent-repo.sh --also-install

Monitoring includes:
- CPU utilization
- Memory usage
- Disk usage
- Uptime check

Backend stability is maintained using PM2 to ensure automatic restart in case of failure.

## Security Configuration

- HTTPS enabled on production domain.
- Firewall rules configured.
- Cloud SQL authorized network restricted.
- Backend accessed internally via reverse proxy.

## Cost Optimization

- VM uses e2-small instance.
- Cloud SQL usage managed to control billing.
- Monitoring within free-tier quota.
- Static IP attached to active VM to avoid unused IP charges.

## Deployment Result

The Data Center application has been successfully deployed on Google Cloud Platform with:

- Backend running on Compute Engine.
- Frontend accessible via custom domain.
- Managed Cloud SQL database integration.
- Reverse proxy configuration using NGINX.
- Active monitoring and uptime checking.
- Stable performance under current workload.
