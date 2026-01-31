Thanks for opening this issue! GitHub Copilot is supported by OpenClaw, but it requires OAuth device flow authentication rather than a simple API key.

**Why it's different from other providers:**
Anthropic, OpenAI, Gemini, etc. use API keys you can paste directly. GitHub Copilot requires an OAuth login flow that exchanges a GitHub token for Copilot API access at runtime.

**Workaround for Docker/Unraid:**

Since the container can't run the interactive OAuth flow, you'll need to do it on another machine first:

**On your Mac or PC (not Unraid):**
1. Install OpenClaw CLI:
   ```bash
   npm install -g openclaw
   ```
2. Run the Copilot login:
   ```bash
   openclaw models auth login-github-copilot
   ```
3. Complete the GitHub device flow (visit URL, enter code)
4. Find the auth profile at:
   ```
   ~/.openclaw/agents/main/agent/auth-profiles.json
   ```

**On Unraid:**
5. Copy that `auth-profiles.json` file to:
   ```
   /mnt/user/appdata/openclaw/config/agents/main/agent/auth-profiles.json
   ```
   (You may need to create the `agents/main/agent/` directories)

6. Update your config via Raw JSON in the Control UI to use Copilot:
   ```json
   {
     "agents": {
       "defaults": {
         "model": { "primary": "github-copilot/gpt-4o" }
       }
     }
   }
   ```

7. Restart the container

**Note:** Copilot model availability depends on your GitHub plan. If a model is rejected, try another ID like `github-copilot/gpt-4.1`.

This is similar to how Claude Pro/Max subscription auth works — you run `claude setup-token` elsewhere and paste the result. Unfortunately, GitHub Copilot doesn't have an equivalent simple token export, so copying the auth profile file is the workaround.

I can't add this directly to the template since it requires the interactive OAuth flow, but hopefully this helps you get it working!

Full docs: https://docs.openclaw.ai/providers/github-copilot
