# Local LLM Server with Ollama & Docker 🚀

A simple Docker setup for running large language models locally on AMD GPUs using Ollama. This setup provides a complete local AI server with web API access.

## 📋 Prerequisites

Before getting started, make sure you have:

- **Docker** and **Docker Compose** installed
- **AMD GPU** with ROCm support

## 🚀 Quick Start

### 1. Start the Server

```bash
docker compose up -d
```

This command will:
- Start the Ollama server with AMD GPU (ROCm) support
- Launch an Nginx reverse proxy for easy access
- Run everything in the background (`-d` flag)

### 2. Verify the Server is Running

Check if the server is healthy:
```bash
docker compose ps
```

You should see both `llmdevosku-llmserver` and `llmdevosku-reverse-proxy` running.

### 3. Access Your Local AI Server

Your Ollama API is now available at: **http://localhost:8020**

## 🤖 Working with Models

### Browse Available Models

- Visit [Ollama Model Library](https://ollama.com/search) to explore available models
- Or check what's already downloaded:
  ```bash
  docker exec llmdevosku-llmserver ollama list
  ```

### Download a Model

To download a new model (this may take several minutes depending on model size):

```bash
# Popular options:
docker exec -it llmdevosku-llmserver ollama pull llama3.2:3b     # Small, fast
docker exec -it llmdevosku-llmserver ollama pull qwen2.5-coder:7b # Good for coding
docker exec -it llmdevosku-llmserver ollama pull llama3.2:8b     # Balanced performance
```

### Chat with a Model

Start an interactive chat session:

```bash
docker exec -it llmdevosku-llmserver ollama run llama3.2:3b
```

Type your questions and press Enter. Type `/bye` to exit the chat.

## 🔧 API Usage

You can interact with your local LLM via HTTP requests:

```bash
# Generate a response
curl -X POST http://localhost:8020/api/generate \
  -H "Content-Type: application/json" \
  -d '{
    "model": "llama3.2:3b",
    "prompt": "Why is the sky blue?",
    "stream": false
  }'
```

## 🛠 Management Commands

### View Logs
```bash
docker compose logs -f
```

### Stop the Server
```bash
docker compose down
```

### Update Ollama
```bash
docker compose pull
docker compose up -d
```

### Clean Up Space
```bash
# Remove unused models
docker exec llmdevosku-llmserver ollama rm <model-name>

# See disk usage
docker exec llmdevosku-llmserver du -sh /root/.ollama
```

## 📁 Data Storage

All model data is stored in the `./data` directory on your host machine, so your models persist between container restarts.

## ⚠️ Troubleshooting

**Server won't start?**
- Check if Docker is running: `docker version`
- Verify AMD GPU access: `ls /dev/dri`

**Out of memory errors?**
- Try smaller models like `llama3.2:1b` or `phi3:mini`
- Monitor GPU memory: `rocm-smi`

**Slow performance?**
- Ensure you're using the ROCm-enabled container
- Check available system RAM (models load into memory)
