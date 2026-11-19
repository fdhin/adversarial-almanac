---
name: "juice-shop"
categories:
  - webapp
version: "14.5.1+node18-0parrot1"
homepage: "https://github.com/juice-shop/juice-shop"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "juice-shop/juice-shop"
github-stars: 14387
github-pushed: "2026-11-04"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2224
gh-activity-checked: "2026-11-09"
---

# juice-shop

> OWASP Juice Shop — an intentionally insecure Node.js shop application used for security trainings, awareness demos, CTFs and as a safe target for testing tools.

## What it's for

Juice Shop is a training artifact, not an attack tool: a modern-looking e-commerce application in which every vulnerability is deliberately planted, spanning the OWASP Top Ten and well beyond. You run an instance locally (or for a class) and attack it — the accompanying companion guide maps each challenge, and a score board in the app tracks which ones have been solved. It is the standard guinea pig for trying out scanners, proxies and manual techniques against something realistic without touching anyone's infrastructure. It is Node.js-based and actively maintained by OWASP; the packaged copy exists so a lab machine can host an offline instance. Do not expose an instance to an untrusted network — it is insecure by design.

## Typical use cases

### Run a local training instance

The fastest way to get a target to practice against.

```
docker run --rm -p 127.0.0.1:3000:3000 bkimminich/juice-shop
```

Outcome: the application comes up on `http://localhost:3000` — browse, register and shop normally, then attack at will; solved challenges register on the in-app score board. Binding to 127.0.0.1 keeps the deliberately vulnerable app off the network; `--rm` throws away challenge progress when the container stops (state lives in the container's database).

### Run the application from its directory

The non-Docker path, for a packaged copy or source tree.

```
npm start
```

Outcome: run from the unpacked application directory (where `package.json` lives), this serves the same app on `http://localhost:3000` — the form used for development, custom forks and the distro-packaged installation.

### Host an instance for a class or CTF

Serve one instance to multiple participants.

```
docker run --rm -p 198.51.100.10:3000:3000 bkimminich/juice-shop
```

Outcome: the instance listens on a LAN-facing address instead of loopback, so every participant attacks the same shop and their challenge completions land on the shared score board. For competitive scoring, upstream documents CTF support (flag codes per challenge, scoreboard export) via its companion guide and the separate CTF extension tooling.

No corpus evidence — derived from upstream documentation (juice-shop/juice-shop README: Docker container, from-source run, CTF support).
