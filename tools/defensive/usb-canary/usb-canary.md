---
name: "usb-canary"
categories:
  - blackarch-defensive
version: "31.bb23552"
homepage: "https://github.com/errbufferoverfl/usb-canary"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "errbufferoverfl/usb-canary"
github-stars: 580
github-pushed: "2017-10-09"
github-archived: true
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# usb-canary

> Detect USB devices plugged in or pulled out while your machine is locked, and alert you by SMS, Slack, or Pushover.

## What it's for

usb-canary is a Python daemon (Linux and macOS) that watches USB device state with psutil and raises an alert when a device is inserted or removed while the machine is locked — a tripwire against implants plugged into an unattended laptop. Notifications go out through Twilio SMS, a Slack bot, or Pushover push messages. A "paranoid" mode monitors around the clock regardless of screensaver state, which suits physically accessible servers; on Linux it auto-detects xscreensaver and gnome-screensaver, and paranoid mode is the fallback when neither is usable. Configuration lives in a `settings.json` file beside the program, and every misconfiguration has a distinct exit code. Upstream is archived (2017) and explicitly not intended for production systems. The BlackArch package exposes it behind a `usb-canary` wrapper command.

## Typical use cases

### Start the canary while you are away

```
usb-canary start
```

Outcome: the daemon starts and monitors USB device state; while the screensaver is locked, any plug or unplug event fires your configured notification channel. `stop` and `restart` subcommands manage the running daemon.

### Watch a server around the clock (paranoid mode)

```
sudo tee /usr/share/usb-canary/settings.json <<'EOF'
{
  "settings": {
    "general": {
      "paranoid": true,
      "screensaver": "xscreensaver",
      "slack": false,
      "twilio": false,
      "pushover": true
    },
    "pushover": {
      "priority": 1,
      "user key": "youruserkeygoeshere",
      "api_token": "yourapitokengoeshere"
    }
  }
}
EOF
usb-canary start
```

Outcome: with `paranoid` set to `true` the daemon ignores screensaver state entirely and alerts on every USB change event, day or night — the upstream-recommended posture for headless or physically exposed machines.

### Triage a failed start by exit code

```
usb-canary start; echo "exit: $?"
```

Outcome: exit 0 means the canary armed successfully; 5xx codes mean a missing or unparseable `settings.json` or an unsupported OS; 4xx codes pinpoint channel and screensaver problems (credentials missing for Slack/Twilio/Pushover, unsupported or conflicting screensaver) — the exit-code table in the upstream README maps each value to the exact setting to fix.

*No corpus evidence — derived from upstream documentation.*
