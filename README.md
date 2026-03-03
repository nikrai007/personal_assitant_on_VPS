Free AI for n8n: Install Ollama on 16GB VPS & Create Docker Network – Part 1

Author: Nikhil
Published: 03-03-2026
Version: 2.0
VPS Provider: Hostinger
Operating System: Ubuntu 22.04 LTS
Primary Model Used: deepseek-coder:6.7b

🚀 Overview

This guide explains how to deploy Ollama inside Docker on a 16GB Ubuntu VPS, configure a private Docker network, install an optimized AI coding model, and prepare the environment for n8n workflows — without API costs.

This setup is production-aware and based on real deployment experience on a 16GB CPU VPS.

🎯 What You'll Learn

Deploy Ollama inside Docker with persistent storage

Create a private Docker network for container communication

Install optimized AI models for 16GB RAM VPS

Avoid memory crashes from oversized models

Secure your AI server properly

Prepare foundation for n8n AI automation workflows

Understand real performance expectations on CPU-only VPS

🖥 VPS Environment Used
Component	Specification
VPS Provider	Hostinger
OS	Ubuntu 22.04
RAM	16GB
Swap	Recommended (16GB)
Deployment	Docker
Model	deepseek-coder:6.7b
API Port	11434 (internal by default)
🧠 Why This Setup?
✅ No API Costs

Run local AI models instead of paying per-token fees to external providers.

✅ Private AI Infrastructure

Models run entirely on your VPS. No data leaves your server.

✅ Agentic Workflow Ready

Supports:

RAG systems

Code generation

Autonomous task planning

Data extraction

Internal automation tools

n8n integrations

⚠ Important Performance Reality (Must Read)

Large models (70B / 80B MoE) will NOT run on 16GB RAM.

Real Error Example
Error: model requires more system memory (50.2 GiB) than is available
Why This Happens

80B MoE models require ~50GB+ RAM

Quantization does NOT magically make them fit

Ollama blocks loading to prevent server crash

📊 Safe Model Size Range for 16GB VPS
Model Size	Status
3B	Very Safe
6–7B	Ideal
13B	Risky
24B+	Not Recommended
80B	Impossible
🛠 Step-by-Step Installation
Step 1 – Confirm Docker Installed
docker --version
docker compose version

If Docker is not installed, install it first.

Step 2 – Create Project Folder
mkdir ollama
cd ollama
Step 3 – Create Shared Docker Network
docker network create App-Net
docker network ls

This network allows:

Ollama

n8n

Vector DB

Future containers

to communicate internally.

Step 4 – Create docker-compose.yml

Create file:

nano docker-compose.yml

Paste:

version: "3.8"

services:
  ollama:
    image: ollama/ollama:latest
    container_name: ollama
    restart: unless-stopped
    networks:
      - app-net
    environment:
      - OLLAMA_HOST=0.0.0.0
    volumes:
      - ollama_data:/root/.ollama

networks:
  app-net:
    external: true
    name: App-Net

volumes:
  ollama_data:

Save and exit Nano.

⚠ No ports exposed yet — this keeps Ollama private.

Step 5 – Start Container
docker compose up -d
docker ps

You should see the ollama container running.

🤖 Install Recommended Model (Production Tested)
deepseek-coder:6.7b

Install:

docker exec -it ollama ollama pull deepseek-coder:6.7b

Run:

docker exec -it ollama ollama run deepseek-coder:6.7b
Why This Model?

Strong coding ability

Stable on CPU-only VPS

Good reasoning

Suitable for agentic workflows

Fits safely within 16GB RAM

🗑 Removing Oversized / Garbage Models

List installed models:

docker exec -it ollama ollama list

Remove model:

docker exec -it ollama ollama rm model-name

Use this if you accidentally downloaded a very large model.

🔐 Security Check (Very Important)

If you DO NOT expose ports in docker-compose:

Your model is NOT publicly accessible.

If you expose port 11434:

ports:
  - "11434:11434"

Then you MUST secure it with:

Firewall rules

Reverse proxy (Nginx)

Authentication layer

IP restrictions

Rate limiting

⚠ Never expose raw AI endpoints publicly without protection.

🧠 Add Swap for Stability (Recommended)
fallocate -l 16G /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile

Make it persistent:

echo '/swapfile none swap sw 0 0' >> /etc/fstab

Verify:

free -h
📊 Expected Performance (CPU VPS)
Metric	Expected
First Token Delay	5–20 sec
Full Response	30–90 sec
CPU Usage	90–100%
RAM Usage	6–10GB

This is completely normal for CPU-based inference.

📡 (Optional) Access via Postman

If exposing API:

Endpoint:

POST http://YOUR_VPS_IP:11434/api/chat

Body:

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

Response will appear inside:

message.content
📁 Final Project Structure (2026)
ollama-vps-setup/
├── README.md
├── docker-compose.yml
├── resources/
│   └── cheatsheet.md
└── examples/
🧩 Part 2 Preview

Next tutorial will cover:

Connecting Ollama to n8n

Building agentic workflows

Tool calling patterns

RAG memory setup

API testing via Postman

Production hardening

🏁 Conclusion

You now have:

Production-ready Ollama Docker deployment

Persistent model storage

Private Docker networking

Optimized model for 16GB VPS

Secure AI foundation for automation

Realistic performance expectations

This is a real infrastructure-level AI setup — not just a demo.

📌 Key Lessons From Real Deployment

Always match model size to RAM.

Quantization does NOT make huge enterprise models fit small VPS.

6–7B models are the sweet spot for 16GB CPU VPS.

Architecture & prompt engineering matter more than raw parameter size.

Swap improves stability but does not replace RAM.

Author: Nikhil
Last Updated: 03-03-2026
Version: 2.0
Status: Production-Ready Foundation Established 🚀
