# 🚀 Private AI Studio

> **A production-ready, fully local AI development environment powered by Ollama and Open WebUI.**
>
> Run modern LLMs completely offline with an OpenAI-compatible API, an intuitive web interface, and persistent Docker storage—perfect for local coding assistants, AI experimentation, and secure development workflows.

---

# Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Architecture](#architecture)
- [Tech Stack](#tech-stack)
- [Prerequisites](#prerequisites)
- [Project Structure](#project-structure)
- [Installation](#installation)
- [Starting the Stack](#starting-the-stack)
- [Downloading Models](#downloading-models)
- [Using Open WebUI](#using-open-webui)
- [API Reference](#api-reference)
  - Native Ollama API
  - OpenAI-Compatible API

- [Examples](#examples)
  - cURL
  - Python
  - JavaScript

- [Remote Access](#remote-access)
- [Docker Volumes](#docker-volumes)
- [Useful Commands](#useful-commands)
- [Troubleshooting](#troubleshooting)
- [Recommended Models](#recommended-models)
- [Security Notes](#security-notes)
- [License](#license)

---

# Overview

Private AI Studio is a lightweight Docker-based AI platform that allows you to run Large Language Models (LLMs) completely on your own machine.

Instead of relying on cloud providers like OpenAI, Anthropic, or Google, every request stays on your hardware.

The stack consists of:

- **Ollama** – Local LLM inference engine
- **Open WebUI** – Beautiful web interface and multi-user AI dashboard

The environment is ideal for:

- AI-assisted programming
- Offline development
- Secure enterprise environments
- Personal AI assistants
- Local RAG projects
- API integrations
- Continue.dev
- VS Code
- Cursor
- Windsurf
- Cline
- Custom applications

---

# Features

## 🔒 100% Private

- No external API calls
- No cloud dependency
- No telemetry required
- Your prompts never leave your computer

---

## ⚡ Fast & Lightweight

Optimized for lightweight coding models such as:

- `qwen2.5-coder:3b`
- ~2.2GB RAM usage
- CPU compatible
- Works great with integrated GPUs

---

## 🤖 OpenAI Compatible

Use the standard OpenAI SDK without changing your application logic.

Simply replace:

```
https://api.openai.com/v1
```

with

```
http://localhost:11434/v1
```

---

## 💾 Persistent Storage

Models and chat history remain available after:

- reboot
- container recreation
- Docker updates

---

## 🌐 Remote Access Ready

Compatible with:

- Tailscale
- Cloudflare Tunnel
- Reverse proxies
- Nginx Proxy Manager
- Caddy

---

## 🧩 IDE Integration

Works with:

- VS Code
- Cursor
- Continue.dev
- Cline
- Windsurf
- OpenAI SDK
- LangChain
- LlamaIndex

---

# Architecture

```
                 Browser
                     │
                     ▼
              Open WebUI
              Port: 3000
                     │
                     ▼
                 Ollama API
              Port: 11434
                     │
                     ▼
          Local AI Models (GGUF)
```

---

# Tech Stack

| Component         | Technology               |
| ----------------- | ------------------------ |
| AI Engine         | Ollama                   |
| Frontend          | Open WebUI               |
| Container Runtime | Docker                   |
| Orchestration     | Docker Compose           |
| API               | REST + OpenAI Compatible |
| Storage           | Docker Volumes           |

---

# Prerequisites

Install:

- Docker
- Docker Compose

Verify installation:

```bash
docker --version
docker compose version
```

---

# Project Structure

```
private-ai-studio/
│
├── docker-compose.yml
├── README.md
│
└── volumes
```

---

# Installation

Create a new directory.

```bash
mkdir private-ai-studio

cd private-ai-studio
```

Create:

```
docker-compose.yml
```

Paste the following configuration.

```yaml
services:
  ollama:
    image: ollama/ollama:latest
    container_name: ollama
    restart: unless-stopped

    ports:
      - "11434:11434"

    volumes:
      - ollama_data:/root/.ollama

  open-webui:
    image: ghcr.io/open-webui/open-webui:main
    container_name: open-webui
    restart: unless-stopped

    ports:
      - "3000:8080"

    environment:
      OLLAMA_BASE_URL: http://ollama:11434

    depends_on:
      - ollama

    volumes:
      - open-webui_data:/app/backend/data

volumes:
  ollama_data:
  open-webui_data:
```

---

# Starting the Stack

Start everything in detached mode.

```bash
docker compose up -d
```

Check running containers.

```bash
docker ps
```

Expected output:

```
ollama
open-webui
```

---

# Downloading Models

Download your preferred model.

Example:

```bash
docker exec -it ollama ollama pull qwen2.5-coder:3b
```

Verify installed models.

```bash
docker exec -it ollama ollama list
```

Example output:

```
NAME

qwen2.5-coder:3b
```

---

# Using Open WebUI

Open your browser.

Local machine

```
http://localhost:3000
```

LAN

```
http://YOUR_SERVER_IP:3000
```

During the first launch:

1. Create an account.
2. The first registered user automatically becomes the administrator.
3. Select your installed model.
4. Start chatting.

---

# API Reference

## Native Ollama API

Base URL

```
http://localhost:11434
```

---

### Chat Completion

```
POST /api/chat
```

Example:

```bash
curl http://localhost:11434/api/chat \
-d '{
    "model":"qwen2.5-coder:3b",
    "messages":[
      {
        "role":"user",
        "content":"Write a Go REST API."
      }
    ],
    "stream":false
}'
```

---

### Generate Text

```
POST /api/generate
```

```bash
curl http://localhost:11434/api/generate \
-d '{
    "model":"qwen2.5-coder:3b",
    "prompt":"Explain Docker volumes.",
    "stream":false
}'
```

---

# OpenAI-Compatible API

Base URL

```
http://localhost:11434/v1
```

API Key

```
ollama
```

> Any non-empty API key works.

---

## Chat Completion

```bash
curl http://localhost:11434/v1/chat/completions \
-H "Content-Type: application/json" \
-d '{
  "model":"qwen2.5-coder:3b",
  "messages":[
      {
        "role":"system",
        "content":"You are a senior software engineer."
      },
      {
        "role":"user",
        "content":"Optimize this React component."
      }
  ]
}'
```

---

# Examples

## Python

```python
from openai import OpenAI

client = OpenAI(
    base_url="http://localhost:11434/v1",
    api_key="ollama"
)

response = client.chat.completions.create(
    model="qwen2.5-coder:3b",
    messages=[
        {
            "role": "user",
            "content": "Write an Express middleware."
        }
    ]
)

print(response.choices[0].message.content)
```

---

## JavaScript

```javascript
import OpenAI from "openai";

const client = new OpenAI({
  baseURL: "http://localhost:11434/v1",
  apiKey: "ollama",
});

const response = await client.chat.completions.create({
  model: "qwen2.5-coder:3b",
  messages: [
    {
      role: "user",
      content: "Create a React Hook.",
    },
  ],
});

console.log(response.choices[0].message.content);
```

---

## cURL

```bash
curl http://localhost:11434/v1/chat/completions \
-H "Content-Type: application/json" \
-d '{
  "model":"qwen2.5-coder:3b",
  "messages":[
      {
        "role":"user",
        "content":"Explain Kubernetes."
      }
  ]
}'
```

---

# Remote Access

## Option 1 — Tailscale (Recommended)

After installing Tailscale:

Open WebUI

```
http://100.x.x.x:3000
```

Ollama API

```
http://100.x.x.x:11434
```

No port forwarding required.

---

## Option 2 — Cloudflare Tunnel

Expose only Open WebUI.

Example domain:

```
https://ai.example.com
```

Protect access using:

- Cloudflare Zero Trust
- Email Authentication
- SSO

---

# Docker Volumes

Persistent volumes:

| Volume          | Purpose                       |
| --------------- | ----------------------------- |
| ollama_data     | Stores downloaded models      |
| open-webui_data | Stores users, chats, settings |

Delete everything:

```bash
docker compose down -v
```

---

# Useful Commands

## Start

```bash
docker compose up -d
```

---

## Stop

```bash
docker compose down
```

---

## Restart

```bash
docker compose restart
```

---

## Logs

```bash
docker compose logs -f
```

---

## Running Containers

```bash
docker ps
```

---

## Installed Models

```bash
docker exec -it ollama ollama list
```

---

## Pull New Model

```bash
docker exec -it ollama ollama pull llama3.2
```

---

## Remove Model

```bash
docker exec -it ollama ollama rm llama3.2
```

---

## Enter Container

```bash
docker exec -it ollama bash
```

---

# Troubleshooting

## Port Already in Use

Check which process is using the port.

```bash
lsof -i :3000

lsof -i :11434
```

---

## Model Not Found

Pull it first.

```bash
docker exec -it ollama ollama pull qwen2.5-coder:3b
```

---

## Container Not Running

Inspect logs.

```bash
docker compose logs
```

---

## Reset Everything

```bash
docker compose down -v

docker compose up -d
```

---

# Recommended Models

| Model            | Purpose               |
| ---------------- | --------------------- |
| qwen2.5-coder:3b | Lightweight coding    |
| qwen2.5-coder:7b | Better coding quality |
| llama3.2         | General assistant     |
| mistral          | Fast reasoning        |
| deepseek-r1      | Reasoning             |
| gemma3           | General purpose       |

---

# Security Notes

For production deployments:

- Never expose port **11434** directly to the public internet.
- Use HTTPS for remote access.
- Place Open WebUI behind a reverse proxy.
- Prefer Tailscale or Cloudflare Tunnel over traditional port forwarding.
- Regularly update Docker images.
- Use firewall rules to restrict unnecessary access.

---

# License

This project is provided as an example setup for self-hosted AI infrastructure.

Feel free to modify, extend, and integrate it into your own projects.

---

# 🎨 Creating a Custom AI Model

Ollama allows you to create custom models based on existing ones. This is useful for branding, predefined system prompts, and custom model parameters.

In this project, we'll create a custom model named **`oziy-ai`** using **`qwen2.5-coder:3b`** as the base model.

> **Note**
>
> Creating a custom model **does not duplicate the model weights**. Ollama simply creates a new model configuration that references the original base model, so it requires very little additional disk space.

---

## Step 1 — Pull the Base Model

If you haven't already downloaded the base model, run:

```bash
docker exec -it ollama ollama pull qwen2.5-coder:3b
```

Verify the installation:

```bash
docker exec -it ollama ollama list
```

Example output:

```text
NAME
qwen2.5-coder:3b
```

---

## Step 2 — Create a Working Directory

Create a new folder on your host machine.

```bash
mkdir oziy-ai
cd oziy-ai
```

---

## Step 3 — Create a `Modelfile`

Create a file named:

```text
Modelfile
```

Paste the following configuration:

```dockerfile
FROM qwen2.5-coder:3b

PARAMETER temperature 0.2
PARAMETER num_ctx 8192

SYSTEM """
You are Oziy AI.

You are an advanced AI coding assistant developed by Ozodbek Alimjonov.

Your specialties include:

- JavaScript
- TypeScript
- React
- Next.js
- Node.js
- Express
- Go
- Python
- Docker
- PostgreSQL
- MongoDB
- AI Engineering
- System Design

Guidelines:

- Always introduce yourself as "Oziy AI".
- Never identify yourself as Qwen unless explicitly asked about your underlying model.
- Produce clean, production-ready code.
- Follow modern best practices.
- Explain your reasoning before writing code when appropriate.
- Prefer TypeScript over JavaScript when possible.
- Keep responses concise unless additional detail is requested.
"""
```

---

## Step 4 — Copy the `Modelfile` into the Ollama Container

Since Ollama is running inside Docker, copy the file into the container.

```bash
docker cp Modelfile ollama:/root/Modelfile
```

Verify that the file exists:

```bash
docker exec -it ollama ls /root
```

Expected output:

```text
Modelfile
```

---

## Step 5 — Build the Custom Model

Create your new model:

```bash
docker exec -it ollama ollama create oziy-ai -f /root/Modelfile
```

If successful, you'll see output similar to:

```text
transferring model data...
creating model...
success
```

---

## Step 6 — Verify the Model

List all installed models:

```bash
docker exec -it ollama ollama list
```

Example output:

```text
NAME
oziy-ai
qwen2.5-coder:3b
```

Your custom model is now ready to use.

---

## Step 7 — Test the Model

Start an interactive session:

```bash
docker exec -it ollama ollama run oziy-ai
```

Example:

```text
>>> Who are you?
```

Expected response:

```text
I am Oziy AI, an advanced AI coding assistant developed by Ozodbek Alimjonov.
How can I help you today?
```

---

## Step 8 — Use in Open WebUI

Refresh Open WebUI:

```text
http://localhost:3000
```

Your new model should now appear in the model selector as:

```text
oziy-ai
```

---

## Step 9 — Use Through the API

Instead of:

```json
{
  "model": "qwen2.5-coder:3b"
}
```

Use:

```json
{
  "model": "oziy-ai"
}
```

### cURL Example

```bash
curl http://localhost:11434/api/chat \
-d '{
  "model":"oziy-ai",
  "messages":[
    {
      "role":"user",
      "content":"Build a REST API in Go."
    }
  ],
  "stream":false
}'
```

### Python Example

```python
from openai import OpenAI

client = OpenAI(
    base_url="http://localhost:11434/v1",
    api_key="ollama"
)

response = client.chat.completions.create(
    model="oziy-ai",
    messages=[
        {
            "role": "user",
            "content": "Write a React Hook."
        }
    ]
)

print(response.choices[0].message.content)
```

### JavaScript Example

```javascript
import OpenAI from "openai";

const client = new OpenAI({
  baseURL: "http://localhost:11434/v1",
  apiKey: "ollama",
});

const response = await client.chat.completions.create({
  model: "oziy-ai",
  messages: [
    {
      role: "user",
      content: "Create an Express middleware.",
    },
  ],
});

console.log(response.choices[0].message.content);
```

---

## Optional: Customize Your AI

You can further customize your model by modifying the `SYSTEM` prompt or adding additional parameters.

Example:

```dockerfile
FROM qwen2.5-coder:3b

PARAMETER temperature 0.2
PARAMETER top_p 0.9
PARAMETER top_k 40
PARAMETER num_ctx 16384
PARAMETER repeat_penalty 1.1

SYSTEM """
You are Oziy AI.

You are a senior software engineer specializing in modern web development and AI engineering.

Always provide production-ready code.
Use clean architecture.
Follow SOLID principles.
Prefer TypeScript when appropriate.
Explain complex topics clearly.
Write secure and optimized solutions.
"""
```

Rebuild the model after making changes:

```bash
docker exec -it ollama ollama create oziy-ai -f /root/Modelfile
```

The existing `oziy-ai` model will be updated with your latest configuration.
