---
name: "telegramrat"
categories:
  - backdoor
version: "git (2025-09-17)"
homepage: "https://github.com/machine1337/telegramrat"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-09"
github-repo: "machine1337/telegramrat"
github-stars: 454
github-pushed: "2025-09-17"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 3
---

# telegramrat

> Cross-platform RAT whose entire command channel is a Telegram bot chat: the implant only ever talks to Telegram's API, so there is no listener port, attacker IP, or C2 domain to hide.

## What it's for

TelegramRAT (machine1337) is a small Windows/Linux remote administration tool whose C2 is a Telegram bot. The client (`client.py`, Python) embeds a bot API token and chat id — create the bot via @BotFather, get the chat id from @chatIDrobot, and edit both into the script — then polls the bot chat for messages, executes them on the host, and returns results, files and screenshots as bot messages. Because the only egress is HTTPS to telegram.org, usually whitelisted and indistinguishable from legitimate messenger use, it sidesteps inbound firewall rules and classic C2 infrastructure detection. The command set is deliberately small: shell commands, `cd`, `download`, `screenshot`, `info`, `location`, and `get <url>`. The author now points users to the successor project pyFUD; treat this as a simple demonstration RAT, not an OPSEC-hardened implant (hardcoded token in the client, no traffic padding or jitter).

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Run the implant on a target

Start the polling client after configuring the bot credentials.

```
python3 client.py
```

Outcome: the client begins polling the Telegram bot whose API token and chat id are set in the script (`python client.py` on Windows); the host is now remote-controlled from that bot chat and no listener exists anywhere.

### Drive a compromised host from the bot chat

Exercise the built-in commands from any Telegram client.

```
info
screenshot
download C:\Users\user\Documents\plans.docx
```

Outcome: messages sent to the bot are executed on the target — system information and a screenshot come back into the chat, and the named file is transferred back through Telegram; `location` returns target geolocation and `get <url>` downloads a file from a URL onto the target.

### Run shell commands directly

Drop to the host's command interpreter through the chat.

```
tasklist
```

Outcome: the command runs on the target via its native command interpreter (cmd on Windows, shell on Linux) and the output is posted back to the Telegram chat.
