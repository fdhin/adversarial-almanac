---
name: "bt2-git"
categories:
  - bluetooth
version: "20160527.r18"
homepage: "https://github.com/blazeinfosec/bt2"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "blazeinfosec/bt2"
github-stars: 207
github-pushed: "2018-09-06"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bt2-git

> Telegram-based backdoor and C2 proof-of-concept that turns a Telegram bot chat into an interactive command channel.

## What it's for

Despite the "bt" prefix, bt2 has nothing to do with Bluetooth: it is the Blaze Telegram Backdoor Toolkit, a Python implant (built on the Telepot library) that polls a Telegram bot for commands and returns output through the same chat, repurposing Telegram's bot API as a command-and-control channel. The appeal is that C2 traffic hides as ordinary HTTPS to a popular messaging domain. You register a bot with Telegram's BotFather, put the bot's key and the botmaster's user ID into `bt2.py` (locking the bot to one operator), run it on the target host, and drive it from any Telegram client. The shellcode-execution component is Windows-only, and the project is an explicit proof of concept for studying IM-based command channels rather than a maintained framework.

## Typical use cases

### Start the implant

Run the backdoor after configuring the bot token and botmaster ID in `bt2.py`.

```
python bt2.py
```

Outcome: the process connects to the Telegram bot API and starts polling; commands sent to the bot chat (shell commands, reverse shell, shellcode execution on Windows) run on the host and their output comes back as chat replies.

*No corpus evidence — derived from upstream documentation.*
