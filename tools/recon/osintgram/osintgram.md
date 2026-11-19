---
name: "osintgram"
categories:
  - blackarch-recon
version: "2.0.r9.gf2c19b5"
homepage: "https://github.com/Datalux/Osintgram"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-10-31"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Datalux/Osintgram"
github-stars: 15468
github-pushed: "2026-10-30"
github-archived: false
github-issues: 7
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 13
gh-activity-checked: "2026-11-09"
---

# osintgram

> Locally-run web application for Instagram account OSINT: 29 lookup commands covering profile, network, content and contact data, optionally driven by a local AI model through Ollama.

## What it's for

Osintgram (Giuseppe Criscione, Datalux) analyzes what a public Instagram profile gives away: bio and links, follower/following counts and lists, related-account suggestions, hashtags ranked by use, captions, comments, like/comment statistics, a weekday-by-hour posting heatmap, geotagged locations on a map, and public emails or phone numbers among a target's followers or followings. Version 2.0 replaced the old interactive terminal shell (the 1.x `main.py` CLI no longer exists in the repository) with a web interface served from `127.0.0.1` — pick commands yourself in base mode, or ask questions in plain language and a local Ollama model picks from the same fixed command list. Data access needs a backend: a HikerAPI key (paid, priced per lookup — the UI shows cost and remaining credit up front and caches requests) or a free `instagrapi` login using an Instagram account you own and are willing to risk; never your primary one. Everything runs on your machine, results export as standalone HTML reports, and it cannot see private profiles. The project states it is for educational purposes, and results contain third-party personal data subject to GDPR-equivalent law.

## Typical use cases

### Run the web interface locally

Serve the app and open it in a browser.

```
uvicorn src.web.app:app --host 127.0.0.1 --port 8000 --reload
```

Outcome: the interface is served at http://127.0.0.1:8000 — the opening panel asks for a HikerAPI key (verified before it is accepted) or an instagrapi login; `make run` is a shortcut for the same command. Keep it bound to localhost: it ships with no authentication.

### Run it in a container

Use the compose setup instead of a local Python environment.

```
docker compose up --build
```

Outcome: same interface at the same `127.0.0.1` address; your API key, cache and saved searches stay on the host in `config/`, `cache/` and `dossier/`, never inside the image.

### Enable AI mode for plain-language queries

Skip picking commands by hand and let a local model select and summarize them.

```
ollama pull llama3.1:8b
```

Outcome: with Ollama running and the default ~4.7 GB model pulled, AI mode accepts questions like "which hashtags do they use most", chooses lookups from the fixed command list, shows the priced run sheet before executing, and returns a written answer with the raw results underneath; base mode works entirely without it.

> No corpus evidence — derived from upstream documentation.
