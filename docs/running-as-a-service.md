# Running AgentTap as a background service

Keep AgentTap running across reboots and logouts so `http://127.0.0.1:4318`
is always there. Pick your platform.

Adjust `--openai-upstream` to your local gateway (LiteLLM/Ollama/NIM) or drop
it to use the default `https://api.openai.com`.

---

## macOS (launchd)

macOS has no `systemctl`; it uses **launchd** with a per-user LaunchAgent.

**1. Generate the LaunchAgent** (this fills in the correct node + AgentTap
paths automatically — just paste it):

```bash
mkdir -p ~/Library/LaunchAgents
NODE="$(command -v node)"
CLI="$(npm root -g)/agenttap/src/cli.js"
cat > ~/Library/LaunchAgents/com.agenttap.plist <<PLIST
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>Label</key><string>com.agenttap</string>
  <key>ProgramArguments</key>
  <array>
    <string>${NODE}</string>
    <string>${CLI}</string>
    <string>--openai-upstream</string>
    <string>http://127.0.0.1:4000</string>
  </array>
  <key>RunAtLoad</key><true/>
  <key>KeepAlive</key><true/>
  <key>StandardOutPath</key><string>/tmp/agenttap.log</string>
  <key>StandardErrorPath</key><string>/tmp/agenttap.log</string>
</dict>
</plist>
PLIST
```

**2. Start it (and enable at login):**

```bash
launchctl bootstrap gui/$(id -u) ~/Library/LaunchAgents/com.agenttap.plist
```

**3. Everyday commands:**

```bash
# restart (after an update, or to pick up config changes)
launchctl kickstart -k gui/$(id -u)/com.agenttap

# stop / remove
launchctl bootout gui/$(id -u)/com.agenttap

# status + logs
launchctl print gui/$(id -u)/com.agenttap | head
tail -f /tmp/agenttap.log
```

**Update on macOS:**

```bash
npm install -g agenttap@latest
launchctl kickstart -k gui/$(id -u)/com.agenttap   # restart to load the new code
```

---

## Linux (systemd user service)

**1. Generate the unit** (auto-fills paths):

```bash
mkdir -p ~/.config/systemd/user
NODE="$(command -v node)"
CLI="$(npm root -g)/agenttap/src/cli.js"
cat > ~/.config/systemd/user/agenttap.service <<UNIT
[Unit]
Description=AgentTap — local agent observability
After=network.target

[Service]
Type=simple
ExecStart=${NODE} ${CLI} --openai-upstream http://127.0.0.1:4000
Restart=always
RestartSec=3

[Install]
WantedBy=default.target
UNIT
```

**2. Enable + start (and survive logout/reboot):**

```bash
loginctl enable-linger "$USER"        # keep the service running when logged out
systemctl --user daemon-reload
systemctl --user enable --now agenttap
```

**3. Everyday commands:**

```bash
systemctl --user restart agenttap
systemctl --user stop agenttap
systemctl --user status agenttap
journalctl --user -u agenttap -f      # follow logs
```

**Update on Linux:**

```bash
npm install -g agenttap@latest
systemctl --user restart agenttap     # restart to load the new code
```

---

## Command cheat-sheet

| Action | Linux (systemd) | macOS (launchd) |
|---|---|---|
| Start / enable | `systemctl --user enable --now agenttap` | `launchctl bootstrap gui/$(id -u) ~/Library/LaunchAgents/com.agenttap.plist` |
| Restart | `systemctl --user restart agenttap` | `launchctl kickstart -k gui/$(id -u)/com.agenttap` |
| Stop | `systemctl --user stop agenttap` | `launchctl bootout gui/$(id -u)/com.agenttap` |
| Status | `systemctl --user status agenttap` | `launchctl print gui/$(id -u)/com.agenttap` |

> **Updating always takes two steps** — `npm install -g agenttap@latest` updates
> the files on disk, but the running process keeps the old code in memory until
> you **restart** the service.
