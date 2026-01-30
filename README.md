# OpenClaw Unraid Template

Community Application template for [OpenClaw](https://github.com/openclaw/openclaw) - your AI-powered personal assistant.

## ⚠️ Important: First-Time Setup

**The container runs as user `node` (UID 1000), NOT root.**

### Step 1: Create directories and config
```bash
mkdir -p /mnt/user/appdata/openclaw/config
mkdir -p /mnt/user/appdata/openclaw/workspace
```

### Step 2: Create the config file
Save this as `/mnt/user/appdata/openclaw/config/openclaw.json`:
```json
{
  "gateway": {
    "mode": "local",
    "bind": "lan",
    "auth": {
      "token": "${OPENCLAW_GATEWAY_TOKEN}"
    }
  },
  "agents": {
    "defaults": {
      "workspace": "/home/node/clawd"
    }
  }
}
```

### Step 3: Fix permissions
```bash
chown -R 1000:1000 /mnt/user/appdata/openclaw
```

### Step 4: Start the container
Via Unraid Docker UI or:
```bash
docker run -d \
  --name openclaw-gateway \
  --hostname $(hostname) \
  --network host \
  --restart unless-stopped \
  -v /mnt/user/appdata/openclaw/config:/home/node/.openclaw:rw \
  -v /mnt/user/appdata/openclaw/workspace:/home/node/clawd:rw \
  -e TZ=America/Chicago \
  -e OPENCLAW_GATEWAY_TOKEN=your-secret-token \
  -e ANTHROPIC_API_KEY=sk-ant-... \
  ghcr.io/openclaw/openclaw:latest \
  node dist/index.js gateway
```

### Step 5: Access the Web UI
```
http://YOUR-SERVER-IP:18789?token=YOUR_GATEWAY_TOKEN
```

## Features

- 🤖 AI assistant with Claude/GPT support
- 💬 Multi-channel: Discord, Telegram, Slack, WhatsApp, Signal, iMessage
- 📁 Local file access and command execution
- 🌐 Web browsing and research
- ⏰ Cron jobs and automation
- 🧠 Memory and context persistence
- 🔧 Extensible skills system
- 🖥️ Web UI for management

## Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `OPENCLAW_GATEWAY_TOKEN` | Yes | Password for Web UI access |
| `ANTHROPIC_API_KEY` | Yes* | Anthropic API key for Claude |
| `OPENAI_API_KEY` | No | OpenAI API key (optional) |
| `TZ` | No | Timezone (default: America/Chicago) |

*Required for Claude models. Get one at [console.anthropic.com](https://console.anthropic.com)

## Volumes

| Container Path | Description |
|----------------|-------------|
| `/home/node/.openclaw` | Config, session data, media cache |
| `/home/node/clawd` | Agent workspace (SOUL.md, MEMORY.md, skills) |
| `/projects` | Optional: projects directory for coding tasks |

**Note:** All paths use `/home/node/` because the container runs as the `node` user (UID 1000), not root.

## Updating

```bash
docker pull ghcr.io/openclaw/openclaw:latest
docker stop openclaw-gateway
docker rm openclaw-gateway
# Then run the docker run command again
```

Or via Unraid Docker UI: Check for updates → Apply.

## Troubleshooting

### "EACCES: permission denied"
Fix permissions:
```bash
chown -R 1000:1000 /mnt/user/appdata/openclaw
```

### "Gateway start blocked: set gateway.mode=local"
Your config file is missing or invalid. Make sure `openclaw.json` exists with `gateway.mode: "local"`.

### "disconnected (1008): control ui requires HTTPS"
Append the token to the URL: `http://IP:18789?token=YOUR_TOKEN`

### Web UI not accessible
Make sure config has `gateway.bind: "lan"` (not localhost).

## Support

- 📖 [Documentation](https://docs.openclaw.dev)
- 💬 [Discord Community](https://discord.com/invite/clawd)
- 🐛 [GitHub Issues](https://github.com/openclaw/openclaw/issues)

## Credits

Template created and tested by [@jdhill777](https://github.com/jdhill777)
