# Production Deployment Guide for Ollama on Hostinger VPS

## Introduction
This guide provides comprehensive steps to deploy Ollama on a Hostinger VPS, integrating best practices for security, monitoring, and troubleshooting.

## Prerequisites
- A Hostinger VPS account.
- Docker and Docker Compose installed.
- Basic knowledge of Linux commands.

## Step 1: Prepare Your VPS
1. **Access your VPS via SSH**:
   ```bash
   ssh username@your_vps_ip
   ```
2. **Update the system**:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```
3. **Install necessary packages**:
   ```bash
   sudo apt install -y curl git
   ```

## Step 2: Set Up Docker and Docker Compose
1. **Install Docker**:
   ```bash
   curl -fsSL https://get.docker.com -o get-docker.sh
   sh get-docker.sh
   ```
2. **Install Docker Compose**:
   ```bash
   sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
   sudo chmod +x /usr/local/bin/docker-compose
   ```

## Step 3: Create Docker Network
Create a dedicated network for your application:
```bash
docker network create app-net
```

## Step 4: Docker Compose Configuration
1. **Create a `docker-compose.yml` file**:
   ```yaml
   version: '3'
   services:
     ollama:
       image: your-ollama-image
       networks:
         - app-net
       ports:
         - "80:80"
       environment:
         - ENV_VAR=value
   networks:
     app-net:
       driver: bridge
   ```
2. **Deploy the application**:
   ```bash
   docker-compose up -d
   ```

## Step 5: Security Hardening
- **Implement a firewall** using UFW:
  ```bash
  sudo ufw allow OpenSSH
  sudo ufw enable
  sudo ufw allow 'Nginx Full' # if Nginx is used
  ```
- **Regularly update your VPS** to patch vulnerabilities.

## Step 6: Monitoring
- **Use Docker logs for monitoring**:
  ```bash
  docker logs -f ollama
  ```
- **Consider integrating monitoring tools** such as Prometheus or Grafana.

## Step 7: Troubleshooting
- **Check Docker container status**:
  ```bash
  docker ps -a
  ```
- **Inspect logs for errors**:
  ```bash
  docker logs ollama
  ```
- **Ensure correct networking**:
  ```bash
  docker network inspect app-net
  ```

## Closing Remarks
Following these steps will help you deploy Ollama efficiently and securely on Hostinger VPS. Make sure to revisit this guide for updates and additional best practices based on your needs.
