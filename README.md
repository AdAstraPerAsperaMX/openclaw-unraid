# OpenClaw for Unraid

[![Unraid](https://img.shields.io/badge/Unraid-CA%20Template-orange)](https://unraid.net/)
[![OpenClaw](https://img.shields.io/badge/OpenClaw-v2026.1.29-blue)](https://github.com/openclaw/openclaw)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

Community Applications template for [OpenClaw](https://github.com/openclaw/openclaw) — an open-source AI-powered personal assistant that runs locally on your Unraid server.

## 🤖 What is OpenClaw?

OpenClaw is a self-hosted AI assistant that connects to your favorite messaging platforms and can:

- 💬 **Chat via Discord, Telegram, Slack, WhatsApp, Signal, or iMessage**
- 📁 **Manage files** — read, write, organize your data
- ⚡ **Execute commands** — run scripts, manage Docker, automate tasks
- 🌐 **Browse the web** — research, fetch data, summarize pages
- ⏰ **Automate with cron** — scheduled tasks and reminders
- 🧠 **Remember context** — persistent memory across conversations
- 🔧 **Extend with skills** — add new capabilities via plugins

**Your data stays on your server.** OpenClaw runs entirely locally — no cloud required.

## 📋 Requirements

- Unraid 6.x or 7.x
- Docker support enabled
- API key from one of:
  - [Anthropic](https://console.anthropic.com) (Claude) — recommended
  - [OpenRouter](https://openrouter.ai) (access to 100+ models)
  - [OpenAI](https://platform.openai.com) (GPT models)

## 🚀 Quick Start

### Step 1: Install from Community Apps

1. Search for **OpenClaw** in Community Applications
2. Click **Install**
3. Fill in the form:
   - **Gateway Token**: Generate with `openssl rand -hex 24` or make up a secure password
   - **Anthropic API Key** (or other LLM key): Your API key
4. Click **Apply**

That's it! The template automatically:
- Creates the config directory
- Sets up the config file with correct settings
- Configures LAN access

### Step 2: Access the Control UI

Open your browser to:

```
http://YOUR-UNRAID-IP:18789/?token=YOUR_GATEWAY_TOKEN
```

**Important:** You must append `?token=YOUR_TOKEN` to the URL for authentication.

Example: `http://192.168.1.41:18789/?token=024c2bdb4da040ac3d3bc632cfe454fdd95d86e978345504`

## ⚙️ Configuration

### Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `OPENCLAW_GATEWAY_TOKEN` | ✅ Yes | Token for Control UI authentication |
| `ANTHROPIC_API_KEY` | ⚡ Recommended | API key for Claude models |
| `OPENROUTER_API_KEY` | ❌ Optional | API key for OpenRouter (100+ models) |
| `OPENAI_API_KEY` | ❌ Optional | API key for GPT models |
| `TZ` | ❌ Optional | Timezone (default: `America/Chicago`) |

*At least one LLM API key is required.*

### Volume Mounts

| Container Path | Host Path | Description |
|----------------|-----------|-------------|
| `/root/.openclaw` | `/mnt/user/appdata/openclaw/config` | Config and session data |
| `/home/node/clawd` | `/mnt/user/appdata/openclaw/workspace` | Agent workspace (files, memory, projects) |

### Adding Channels (Discord, Telegram, etc.)

After installation, you can configure messaging channels via the Control UI, or edit the config file directly at:

```
/mnt/user/appdata/openclaw/config/openclaw.json
```

Example adding Discord:
```json
{
  "gateway": { ... },
  "channels": {
    "discord": {
      "enabled": true,
      "token": "YOUR_DISCORD_BOT_TOKEN"
    }
  }
}
```

See [OpenClaw Docs](https://docs.openclaw.ai/channels) for full channel setup guides.

## 🔄 Updating

Via Unraid Docker UI:
1. Go to Docker tab
2. Click the OpenClaw icon → Check for Updates
3. Apply update

Via command line:
```bash
docker pull ghcr.io/openclaw/openclaw:latest
docker restart OpenClaw
```

## 🔧 Troubleshooting

### "disconnected (1008): control ui requires HTTPS or localhost"

Make sure you're accessing the URL with your token:
```
http://YOUR-IP:18789/?token=YOUR_TOKEN
```

If the error persists, check that the config file exists:
```bash
cat /mnt/user/appdata/openclaw/config/openclaw.json
```

It should contain `"allowInsecureAuth": true`.

### Web UI shows blank page or won't load

Check the container logs:
```bash
docker logs OpenClaw
```

### "Missing config" error in logs

The config file wasn't created. Manually create it:
```bash
mkdir -p /mnt/user/appdata/openclaw/config
echo '{"gateway":{"mode":"local","bind":"lan","controlUi":{"allowInsecureAuth":true},"auth":{"mode":"token"}}}' > /mnt/user/appdata/openclaw/config/openclaw.json
docker restart OpenClaw
```

### Container keeps restarting

Check logs for specific errors:
```bash
docker logs OpenClaw 2>&1 | tail -50
```

## 🖥️ Manual Installation

If you prefer not to use the CA template:

```bash
# Create directories
mkdir -p /mnt/user/appdata/openclaw/config
mkdir -p /mnt/user/appdata/openclaw/workspace

# Create config
cat > /mnt/user/appdata/openclaw/config/openclaw.json << 'EOF'
{
  "gateway": {
    "mode": "local",
    "bind": "lan",
    "controlUi": {
      "allowInsecureAuth": true
    },
    "auth": {
      "mode": "token"
    }
  }
}
EOF

# Run container
docker run -d \
  --name OpenClaw \
  --network host \
  --user root \
  --restart unless-stopped \
  -v /mnt/user/appdata/openclaw/config:/root/.openclaw:rw \
  -v /mnt/user/appdata/openclaw/workspace:/home/node/clawd:rw \
  -e TZ=America/Chicago \
  -e OPENCLAW_GATEWAY_TOKEN=your-secret-token \
  -e ANTHROPIC_API_KEY=sk-ant-... \
  ghcr.io/openclaw/openclaw:latest \
  node dist/index.js gateway --bind lan
```

## 📚 Resources

- **OpenClaw Documentation:** https://docs.openclaw.ai
- **OpenClaw GitHub:** https://github.com/openclaw/openclaw
- **OpenClaw Discord:** https://discord.com/invite/clawd

## 📝 License

This template is released under the [MIT License](LICENSE).

OpenClaw itself is open-source — see the [OpenClaw repository](https://github.com/openclaw/openclaw) for its license.

## 🙏 Credits

- **OpenClaw** — The team behind the AI assistant
- **Template Author** — [@jdhill777](https://github.com/jdhill777)
- **Tested on** — Unraid 7.x

---

**Questions?** Open an issue or join the [OpenClaw Discord](https://discord.com/invite/clawd).
