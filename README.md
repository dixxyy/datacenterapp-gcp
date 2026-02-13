# Data Center Application Deployment on Google Cloud Platform

This repository documents the deployment process of a web-based Data Center application on Google Cloud Platform (GCP). The system is deployed using a cloud-based architecture that separates the application layer and the database layer to improve reliability and scalability.

## Live Deployment

Frontend (Production Domain)
https://ds-datacenter.my.id/

Backend (Public IP Access)
http://34.101.201.82:1337/dashboard

---

## System Interface

### Frontend Dashboard (DCIM Infrastructure Dashboard)

The frontend application is accessible through HTTPS domain and serves as the main monitoring dashboard for Data Center infrastructure.

<img width="1919" height="980" alt="image" src="https://github.com/user-attachments/assets/ed8d533a-df4c-4ba6-97fe-0a5252e27c5b" />


Features:
- Total devices monitoring
- Power usage tracking
- Rack power visualization
- Device type distribution chart
- Real-time infrastructure overview

---

### Backend Admin Panel (Strapi CMS)

The backend is powered by Strapi and provides content management and API control for the frontend dashboard.

<img width="1919" height="982" alt="image" src="https://github.com/user-attachments/assets/b10c8912-744c-42a1-bd67-e34ff725c441" />


Backend Access:
http://34.101.201.82:1337/dashboard/auth/login

Backend Responsibilities:
- API management
- Database integration
- Content management
- Authentication control

---

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

---

## Infrastructure Specification

Machine Type : e2-small  
vCPU         : 2  
Memory       : 2 GB  
OS           : Ubuntu 22.04 LTS  
Region       : us-west1  
External IP  : Static  

---

## Deployment Summary

- Backend deployed via Google Cloud SDK CLI
- Frontend deployed via SSH using GitHub repository
- Reverse proxy configured using NGINX
- Cloud SQL configured as managed database
- Monitoring enabled using Google Cloud Ops Agent
- Uptime check configured for domain reliability

---

## Monitoring and Reliability

Google Cloud Ops Agent installed to monitor:
- CPU utilization
- Memory usage
- Disk usage

Backend managed using PM2 to ensure automatic restart in case of failure.

---

## Security Configuration

- HTTPS enabled on production domain
- Firewall rules configured
- Cloud SQL authorized network restricted
- Backend not directly exposed to public domain
