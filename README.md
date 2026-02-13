# Data Center Monitoring System
Cloud Deployment on Google Cloud Platform

------------------------------------------------------------
PROJECT OVERVIEW
------------------------------------------------------------

This project documents the full production-style deployment of a
Data Center Monitoring System on Google Cloud Platform (GCP).

The system consists of:

- Frontend Dashboard (Static Build - TailAdmin UI)
- Backend API (Strapi - Node.js)
- Database Layer (Cloud SQL - MySQL 8.0)
- Reverse Proxy (NGINX)
- Process Manager (PM2)
- Monitoring and Alerting (Google Cloud Monitoring + Ops Agent)
- HTTPS Security (Certbot)

The architecture follows a Two-Tier Cloud Design where the
application layer and database layer are separated to improve
scalability, reliability, and maintainability.

------------------------------------------------------------
LIVE DEPLOYMENT
------------------------------------------------------------

Frontend (Production HTTPS Domain)
https://ds-datacenter.my.id/

Backend Admin Panel
http://34.101.201.82:1337/dashboard

------------------------------------------------------------
SYSTEM ARCHITECTURE
------------------------------------------------------------

User
  ->
HTTPS (NGINX Reverse Proxy)
  ->
Frontend (Static Build)
  ->
Backend (Strapi - Port 1337)
  ->
Cloud SQL (MySQL 8.0)

Architecture Type  : Two-Tier
Compute Layer      : Google Compute Engine
Database Layer     : Cloud SQL (Managed Service)

------------------------------------------------------------
INFRASTRUCTURE SPECIFICATION
------------------------------------------------------------

VM Name       : strapi-data-center
Machine Type  : e2-small
vCPU          : 2
Memory        : 2 GB
OS            : Ubuntu 22.04 LTS
Region        : asia-southeast2-a
IP Type       : Static External IP
Database      : Cloud SQL MySQL 8.0

------------------------------------------------------------
FULL DEPLOYMENT PROCESS
------------------------------------------------------------

STEP 1 – CREATE VM INSTANCE (CLI)

gcloud compute instances create strapi-data-center \
  --machine-type=e2-small \
  --zone=asia-southeast2-a \
  --image-family=ubuntu-2204-lts \
  --image-project=ubuntu-os-cloud

Open firewall rules:

gcloud compute firewall-rules create allow-http --allow tcp:80
gcloud compute firewall-rules create allow-https --allow tcp:443
gcloud compute firewall-rules create allow-backend --allow tcp:1337

Connect via SSH:

gcloud compute ssh strapi-data-center --zone=asia-southeast2-a

------------------------------------------------------------

STEP 2 – SERVER ENVIRONMENT SETUP

sudo apt update && sudo apt upgrade -y

Install Node.js (LTS)

curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install nodejs -y

Install required packages:

sudo apt install git nginx -y
sudo npm install -g pm2

Enable NGINX:

sudo systemctl enable nginx
sudo systemctl start nginx

------------------------------------------------------------

STEP 3 – CREATE CLOUD SQL (MySQL 8.0)

1. Open Google Cloud Console
2. Navigate to SQL
3. Create MySQL 8.0 instance
4. Select same region as VM
5. Enable Public IP
6. Add VM external IP to Authorized Networks
7. Create database: datacenter_db
8. Create application user

This ensures database isolation and managed reliability.

------------------------------------------------------------

STEP 4 – BACKEND DEPLOYMENT (STRAPI)

Clone backend repository:

git clone https://github.com/your-username/backend-repo.git
cd backend-repo

Install dependencies:

npm install

Create .env file:

HOST=0.0.0.0
PORT=1337
DATABASE_CLIENT=mysql
DATABASE_HOST=CLOUD_SQL_IP
DATABASE_PORT=3306
DATABASE_NAME=datacenter_db
DATABASE_USERNAME=your_user
DATABASE_PASSWORD=your_password
JWT_SECRET=your_secret

Build backend:

npm run build

Run with PM2:

pm2 start npm --name "datacenter-backend" -- start
pm2 startup
pm2 save

Backend now runs on port 1337.

------------------------------------------------------------

STEP 5 – FRONTEND DEPLOYMENT

Clone frontend repository:

git clone https://github.com/your-username/frontend-repo.git
cd frontend-repo

Install dependencies:

npm install

Build production:

npm run build

Deploy static build:

sudo cp -r dist/* /usr/share/nginx/html/

Restart NGINX:

sudo systemctl restart nginx

------------------------------------------------------------

STEP 6 – NGINX REVERSE PROXY CONFIGURATION

Create configuration file:

sudo nano /etc/nginx/sites-available/datacenter

server {
    listen 80;
    server_name ds-datacenter.my.id;

    location / {
        root /usr/share/nginx/html;
        index index.html;
        try_files $uri /index.html;
    }

    location /api {
        proxy_pass http://localhost:1337;
        proxy_http_version 1.1;
        proxy_set_header Host $host;
    }
}

Enable configuration:

sudo ln -s /etc/nginx/sites-available/datacenter /etc/nginx/sites-enabled/

Test configuration:

sudo nginx -t

Restart:

sudo systemctl restart nginx

------------------------------------------------------------

STEP 7 – ENABLE HTTPS (SSL)

Install Certbot:

sudo apt install certbot python3-certbot-nginx -y

Generate certificate:

sudo certbot --nginx -d ds-datacenter.my.id

Choose automatic redirect from HTTP to HTTPS.

------------------------------------------------------------

STEP 8 – MONITORING AND RELIABILITY

Install Google Cloud Ops Agent:

curl -sSO https://dl.google.com/cloudagents/add-google-cloud-ops-agent-repo.sh
sudo bash add-google-cloud-ops-agent-repo.sh --also-install

Monitoring includes:
- CPU utilization
- Memory usage
- Disk usage

Create Uptime Check:
- Target: https://ds-datacenter.my.id
- Interval: 1 minute
- Configure alert policy
- Add email notification

Backend stability ensured using PM2 automatic restart.

------------------------------------------------------------
APPLICATION SCREENSHOTS
------------------------------------------------------------

Frontend – Data Center Dashboard

<img width="1919" height="980" alt="image" src="https://github.com/user-attachments/assets/8f75cc9e-25b8-42af-973d-b4e004de9909" />


Backend – Strapi Admin Panel

<img width="1919" height="982" alt="image" src="https://github.com/user-attachments/assets/0a995d2f-dd37-4126-8c23-bb55ba41cd58" />


Make sure screenshots are stored in:

project-root/
  └── screenshots/
      ├── frontend-dashboard.png
      └── backend-login.png

------------------------------------------------------------
SECURITY CONFIGURATION
------------------------------------------------------------

- HTTPS enabled on production domain
- Firewall rules configured
- Cloud SQL authorized network restricted
- Backend protected behind reverse proxy
- Process isolation using PM2

------------------------------------------------------------
PROJECT SUMMARY
------------------------------------------------------------

This project demonstrates a complete cloud deployment of a
Data Center Monitoring System using Google Cloud Platform.

Key achievements:

- Two-tier cloud architecture implementation
- Managed Cloud SQL integration
- Secure HTTPS-enabled production domain
- Reverse proxy configuration with NGINX
- Backend process management using PM2
- Real-time monitoring and uptime alerting
- Stable system performance under current workload

The deployment reflects practical cloud engineering skills including
infrastructure provisioning, system configuration, backend deployment,
reverse proxy setup, database integration, monitoring implementation,
and production hardening practices.
