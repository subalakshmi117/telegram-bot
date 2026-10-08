# 🤖 Telegram AI Chatbot (n8n + Gemini)

A Telegram chatbot built with **n8n**, powered by **Google Gemini**, with conversation memory. Runs locally using **Docker** and is exposed to Telegram through a **Cloudflare Tunnel**.

## ✨ Features

- Replies to Telegram messages using Google Gemini
- Remembers recent conversation per user (Simple Memory)
- Runs fully on your own machine
- Free to run (n8n self-hosted, Cloudflare quick tunnel, Gemini free tier)

## 🧩 Workflow

```
Telegram Trigger → AI Agent → Send a text message
                     ├── Google Gemini Chat Model
                     └── Simple Memory
```

## 🛠️ Tech Stack

- [n8n](https://n8n.io) (workflow automation)
- Google Gemini API
- Telegram Bot API
- Docker
- Cloudflare Tunnel (`cloudflared`)

## 📋 Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- [cloudflared](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/downloads/)
- A Telegram bot token from [@BotFather](https://t.me/BotFather)
- A Gemini API key from [Google AI Studio](https://aistudio.google.com/apikey)

## 🚀 Setup

### 1. Start the Cloudflare tunnel

```bash
cloudflared tunnel --url http://localhost:5678
```

Copy the generated URL (`https://xxxx.trycloudflare.com`).

### 2. Run n8n with Docker

```bash
docker run -d --name n8n -p 5678:5678 \
  -e WEBHOOK_URL="https://xxxx.trycloudflare.com/" \
  -v n8n_data:/home/node/.n8n \
  docker.n8n.io/n8nio/n8n
```

Open `http://localhost:5678`.

### 3. Configure the workflow

1. Add credentials: **Telegram API** (bot token) and **Google Gemini (PaLM) API** (API key).
2. Import `workflow.json` (if included) or build the nodes as below.

| Node | Setting |
|------|---------|
| Telegram Trigger | Updates: `message` |
| AI Agent | Prompt: `{{ $json.message.text }}` |
| Google Gemini Chat Model | Choose a currently available Gemini model |
| Simple Memory | Key: `{{ $('Telegram Trigger').item.json.message.chat.id }}` |
| Send a text message | Chat ID: `{{ $('Telegram Trigger').item.json.message.chat.id }}`, Text: `{{ $json.output }}` |

### 4. Publish

Click **Publish** in n8n, then message your bot on Telegram.

## ⚠️ Notes

- Free Cloudflare quick tunnels give a **new URL each restart**. After restarting the tunnel, recreate the container with the new `WEBHOOK_URL` and publish the workflow again.
- Keep Docker Desktop and the tunnel running for the bot to respond.
- Simple Memory is stored in the n8n instance and is not suited for production scaling.
- Gemini model names change over time. If you see a 404 error, select a newer model.

## 🔒 Security

Never commit API keys, bot tokens, or exported credentials. n8n stores credentials in its own data volume, not in this repository.

## 📄 License

MIT
```

**Gnabagam:**
- `workflow.json` nu oru file add pannina, n8n la **⋯ menu → Download** pannitu repo la podunga. Illa-na "Import `workflow.json`" line-a delete pannunga.
- Bot token, API key README la podaadheenga.
- Venumna `MIT` license badhula vera license podalaam.
