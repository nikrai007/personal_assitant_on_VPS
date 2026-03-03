# 🚀 Production Deployment Guide

# Ollama on 16GB Ubuntu VPS (Hostinger)

------------------------------------------------------------------------

## 📌 Overview

This document provides a professional, production-oriented guide for
deploying Ollama on a 16GB Ubuntu VPS, securing it properly, and
accessing it remotely using Postman from another system.

This setup is optimized for CPU-based inference and medium-sized coding
models (6B--7B range).

------------------------------------------------------------------------

# 🖥️ Infrastructure Details

  Component           Specification
  ------------------- --------------------
  VPS Provider        Hostinger
  OS                  Ubuntu 22.04 LTS
  RAM                 16GB
  Swap                Recommended (16GB)
  Deployment Method   Docker
  AI Runtime          Ollama
  API Port            11434

------------------------------------------------------------------------

# 1️⃣ Initial Server Access

Login to VPS:

``` bash
ssh root@YOUR_VPS_IP
```

Update system:

``` bash
apt update && apt upgrade -y
```

Install Docker:

``` bash
curl -fsSL https://get.docker.com | sh
systemctl enable docker
systemctl start docker
```

Verify:

``` bash
docker --version
```

------------------------------------------------------------------------

# 2️⃣ Deploy Ollama via Docker

Create docker-compose.yml:

``` yaml
version: "3.8"

services:
  ollama:
    image: ollama/ollama:latest
    container_name: ollama
    restart: unless-stopped
    ports:
      - "11434:11434"
    environment:
      - OLLAMA_HOST=0.0.0.0
    volumes:
      - ollama_data:/root/.ollama

volumes:
  ollama_data:
```

Start service:

``` bash
docker compose up -d
```

Verify:

``` bash
docker ps
```

------------------------------------------------------------------------

# 3️⃣ Add Swap (Recommended for Stability)

``` bash
fallocate -l 16G /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
```

Make persistent:

``` bash
echo '/swapfile none swap sw 0 0' >> /etc/fstab
```

Check:

``` bash
free -h
```

------------------------------------------------------------------------

# 4️⃣ Install Recommended Model (For 16GB CPU VPS)

Example:

``` bash
docker exec -it ollama ollama pull deepseek-coder:6.7b
```

Run:

``` bash
docker exec -it ollama ollama run deepseek-coder:6.7b
```

------------------------------------------------------------------------

# 5️⃣ Expose API for External Access

Ensure firewall allows port 11434:

``` bash
ufw allow 11434
ufw enable
```

Test locally:

``` bash
curl http://localhost:11434/api/tags
```

Test externally:

``` bash
curl http://YOUR_VPS_IP:11434/api/tags
```

------------------------------------------------------------------------

# 6️⃣ Access Ollama from Postman (Remote System)

### Endpoint

POST\
http://YOUR_VPS_IP:11434/api/chat

### Headers

Content-Type: application/json

### Body (JSON)

``` json
{
  "model": "deepseek-coder:6.7b",
  "messages": [
    {
      "role": "user",
      "content": "Build a production agentic framework in Python"
    }
  ],
  "stream": false
}
```

Response will appear inside:

    message.content

------------------------------------------------------------------------

# 7️⃣ Performance Expectations (CPU-Only VPS)

  Metric              Expected
  ------------------- ------------
  First Token Delay   5--20 sec
  Full Response       30--90 sec
  CPU Usage           90--100%
  RAM Usage           6--10GB

This is normal for CPU inference.

------------------------------------------------------------------------

# 8️⃣ Security Best Practices (Production)

⚠️ Do NOT expose raw port 11434 in production without protection.

Recommended:

-   Reverse Proxy (Nginx)
-   Basic Auth or JWT Authentication
-   Rate Limiting
-   Fail2Ban
-   Firewall Restrictions (allow specific IP only)

------------------------------------------------------------------------

# 9️⃣ Removing Large / Failed Models

List models:

``` bash
docker exec -it ollama ollama list
```

Remove model:

``` bash
docker exec -it ollama ollama rm MODEL_NAME
```

------------------------------------------------------------------------

# 🔟 Troubleshooting

### Error: Model requires more memory

Cause: Model too large for 16GB RAM\
Solution: Use 6B--7B models only

### Connection Refused

-   Check docker container running
-   Check firewall
-   Verify port mapping

------------------------------------------------------------------------

# 📊 Recommended Model Sizes for 16GB VPS

  Model Size   Status
  ------------ -----------------
  3B           Very Safe
  6--7B        Ideal
  13B          Risky
  24B+         Not Recommended
  80B          Impossible

------------------------------------------------------------------------

# 🏗 Architecture Diagram

Remote System (Postman) ↓ Public VPS IP ↓ Docker Container (Ollama) ↓
Coding Model (6.7B)

------------------------------------------------------------------------

# 🎯 Conclusion

This setup provides:

-   Stable CPU-based LLM inference
-   Remote API access
-   Dockerized deployment
-   Persistent model storage
-   Production-ready base for agentic AI systems

------------------------------------------------------------------------

# 📌 Recommended Next Steps

-   Add HTTPS using Nginx + SSL
-   Add authentication middleware
-   Build FastAPI wrapper
-   Integrate vector database for RAG
-   Deploy monitoring (Prometheus + Grafana)

------------------------------------------------------------------------

**Production Base Infrastructure Successfully Established 🚀**
