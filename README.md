# ☁️ Cloud Deployment — Data Center Monitoring Application

This repository documents the end-to-end setup and deployment of a data center monitoring application on **Google Cloud Platform (GCP)**.  
It covers local development, cloud infrastructure provisioning, backend and frontend deployment, reverse proxy configuration, security implementation, and monitoring.

---

## 🧭 Overview

The project aims to provide an online dashboard for monitoring data center resources.  
By leveraging cloud infrastructure, the system improves accessibility, scalability, and centralized management.

---

## 🧱 Technology Stack

- Google Cloud Platform — Compute Engine  
- Ubuntu Server  
- Node.js  
- Strapi (Backend API)  
- React / TailAdmin (Frontend Dashboard)  
- Nginx (Reverse Proxy)  
- Let’s Encrypt SSL  
- PM2 (Process Manager)

---

## 📥 Getting Started — Clone & Run Locally

This section allows developers to run the application locally before deploying to the cloud.

### Clone Backend

```bash
git clone <backend-repository>
cd backend
npm install
```

Run development server:

```bash
npm run develop
```

Build production:

```bash
npm run build
npm start
```

---

### Clone Frontend

```bash
git clone <frontend-repository>
cd frontend
npm install
```

Run development:

```bash
npm run dev
```

Build production:

```bash
npm run build
```

---

### Environment Variables

Backend example:

```env
HOST=0.0.0.0
PORT=1337
NODE_ENV=development
```

Frontend example:

```env
VITE_API_URL=http://localhost:1337/api
```

---

## ☁️ Infrastructure Provisioning (GCP)

Create a virtual machine:

- Ubuntu 22.04 LTS  
- e2-micro (or equivalent)  
- Allow HTTP & HTTPS  
- Configure static external IP (recommended)

Connect via SSH:

```bash
gcloud compute ssh <vm-name>
```

---

## ⚙️ Server Preparation

Update system:

```bash
sudo apt update && sudo apt upgrade -y
```

Install dependencies:

```bash
sudo apt install nodejs npm nginx git -y
```

Install PM2:

```bash
sudo npm install -g pm2
```

Verify installation:

```bash
node -v
npm -v
nginx -v
```

---

## 🚀 Backend Deployment (Cloud)

```bash
git clone <backend-repository>
cd backend
npm install
npm run build
pm2 start npm --name backend -- start
pm2 save
```

Example production env:

```env
HOST=0.0.0.0
PORT=1337
NODE_ENV=production
```

---

## 🌐 Frontend Deployment (Cloud)

```bash
git clone <frontend-repository>
cd frontend
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

Create config:

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

## 🔐 SSL Configuration

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

Check services:

```bash
pm2 status
```

View logs:

```bash
pm2 logs
```

Monitor resources:

```bash
htop
```

Suggested metrics:
- CPU  
- Memory  
- Disk  
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

All installation, configuration, and troubleshooting steps are recorded to ensure reproducibility and long-term maintenance.

---

## ✅ Implementation Result

The application is successfully deployed on cloud infrastructure and accessible securely via HTTPS.  
The system enables centralized monitoring and supports scalable future development.

---

## 🔮 Future Improvements

- Docker containerization  
- CI/CD pipeline  
- Advanced monitoring & alerting  
- Scalable architecture

---

## 👤 Author

Dicky Saputra  
Informatics — Universitas Sultan Ageng Tirtayasa
