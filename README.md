# ☁️ Cloud Deployment — Data Center Monitoring Application

This repository documents the end-to-end deployment of a data center monitoring application on **Google Cloud Platform**.  
The deployment covers infrastructure provisioning, backend and frontend setup, reverse proxy configuration, security implementation, and monitoring.

---

## 🧭 Overview

The objective of this deployment is to provide an online monitoring dashboard that improves accessibility, scalability, and centralized system management.  
The application is hosted on a cloud virtual machine and exposed securely via HTTPS.

---

## 🧱 Technology Stack

- Google Cloud Platform — Compute Engine  
- Ubuntu Server  
- Node.js  
- Strapi (Backend API)  
- React / TailAdmin (Frontend Dashboard)  
- Nginx (Reverse Proxy)  
- Let’s Encrypt (SSL)  
- PM2 (Process Manager)

---

## ☁️ Infrastructure Provisioning

### Create Virtual Machine
- OS: Ubuntu 22.04 LTS  
- Machine Type: e2-micro (or equivalent)  
- Allow HTTP and HTTPS traffic  
- Configure static external IP (recommended)

Connect using SSH:

```bash
gcloud compute ssh <vm-name>
```

---

## ⚙️ Server Preparation

Update packages:

```bash
sudo apt update && sudo apt upgrade -y
```

Install dependencies:

```bash
sudo apt install nodejs npm nginx git -y
```

Install process manager:

```bash
sudo npm install -g pm2
```

Verify:

```bash
node -v
npm -v
nginx -v
```

---

## 🚀 Backend Deployment (Strapi)

Clone backend:

```bash
git clone <backend-repository>
cd backend
```

Install dependencies:

```bash
npm install
```

Build and start:

```bash
npm run build
pm2 start npm --name backend -- start
pm2 save
```

Example environment configuration:

```env
HOST=0.0.0.0
PORT=1337
NODE_ENV=production
```

---

## 🌐 Frontend Deployment

Clone frontend:

```bash
git clone <frontend-repository>
cd frontend
```

Install and build:

```bash
npm install
npm run build
```

Deploy static build:

```bash
sudo mkdir -p /var/www/app
sudo cp -r build/* /var/www/app
```

---

## 🔁 Nginx Reverse Proxy

Create configuration:

```bash
sudo nano /etc/nginx/sites-available/app
```

Example:

```nginx
server {
  server_name example.com;

  location / {
    root /var/www/app;
    index index.html;
    try_files $uri /index.html;
  }

  location /api/ {
    proxy_pass http://localhost:1337;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
    proxy_set_header Host $host;
  }
}
```

Enable site:

```bash
sudo ln -s /etc/nginx/sites-available/app /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

---

## 🔐 Security — SSL Setup

Install Certbot:

```bash
sudo apt install certbot python3-certbot-nginx -y
```

Generate certificate:

```bash
sudo certbot --nginx -d example.com
```

Test renewal:

```bash
sudo certbot renew --dry-run
```

---

## 📊 Monitoring & Observability

Check running services:

```bash
pm2 status
```

View logs:

```bash
pm2 logs
```

Monitor server resources:

```bash
htop
```

Suggested metrics:
- CPU usage  
- Memory consumption  
- Disk capacity  
- Application uptime

---

## 🧪 Verification Checklist

- Frontend accessible via domain  
- Backend API responding  
- HTTPS active  
- Dashboard functional  
- Services running via PM2  

---

## 📚 Documentation Strategy

All deployment steps, configuration details, and troubleshooting notes are recorded to ensure reproducibility and support long-term maintenance.

---

## ✅ Implementation Result

The data center monitoring application is successfully deployed on cloud infrastructure and accessible through a secure HTTPS connection.  
The system enables centralized monitoring and supports scalable future development.

---

## 🔮 Future Improvements

- Containerization using Docker  
- CI/CD pipeline integration  
- Advanced monitoring and alerting  
- Horizontal scaling architecture  

---

## 👤 Author

Dicky Saputra  
Informatics — Universitas Sultan Ageng Tirtayasa
