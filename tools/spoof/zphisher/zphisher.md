---
name: "zphisher"
categories:
  - spoof
version: "git (2024-08-21)"
homepage: "https://github.com/htr-tech/zphisher"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "htr-tech/zphisher"
github-stars: 17200
github-pushed: "2024-08-21"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
worth: 4.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 516
---

# zphisher

> Beginner-friendly automated phishing toolkit in Bash — 30+ ready login-page templates with local or tunneled hosting and credential capture out of the box.

## What it's for

Spins up convincing clones of popular sites (social media, mail providers, payment and streaming services, ...) with near-zero configuration: an interactive menu walks you through picking a template, a port, and a tunneling option — plain localhost, Cloudflared, or LocalXpose — then serves the cloned login page and collects submitted credentials for you. Mask URL support generates a less suspicious link, and a Docker image is published for containerized runs. It demonstrates how credential phishing works for awareness and lab purposes (the project is explicit that it is educational-only); it is a static page cloner, not a reverse-proxy MitM framework, so it captures what the victim submits rather than live session tokens. Captured credentials land in the tool's `auth` directory.

## Typical use cases

### Launch the phishing toolkit interactively

```bash
./zphisher.sh
```

Outcome: the menu appears — pick an attack template, the port to serve on, and the tunneling method; the local web server starts and the chosen URL (local, or the Cloudflared/LocalXpose tunnel) is printed. When a victim submits the cloned login form, the credentials are written to the `auth` directory and shown in the session log.

### Run it containerized

```bash
docker run --rm -ti htrtech/zphisher
```

Outcome: the same interactive menu inside the published Docker image, no host setup beyond Docker itself; mount the container's `auth` directory (`-v`) if you want the captured credentials persisted on the host after the container exits.

*No corpus evidence — derived from upstream documentation.*
