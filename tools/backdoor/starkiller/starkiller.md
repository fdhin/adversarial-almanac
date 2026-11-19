---
name: "starkiller"
categories:
  - backdoor
version: "3.0.0-rc1+ds-0parrot1"
homepage: "https://github.com/BC-SECURITY/Starkiller"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot, nixpkgs]
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "BC-SECURITY/Starkiller"
github-stars: 1697
github-pushed: "2026-09-08"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 130
gh-activity-checked: "2026-11-09"
---

# starkiller

> Official web GUI for PowerShell Empire — a VueJS application served by Empire's API that replaces (or complements) the command-line Empire client.

## What it's for

Starkiller (BC-SECURITY) is the official graphical front-end for [[powershell-empire]]: a VueJS web application that drives Empire through its REST API to manage listeners, stagers, agents, modules, malleable profiles, credentials, plugins and operator users. Since Empire 5.0 it ships inside Empire as a submodule — starting the Empire server also serves the UI, so there is nothing extra to install or configure; it can fully replace the `empire` CLI client or run alongside it with mixed GUI and CLI operators on one server. A handful of conveniences (process browser, in-browser module script editing, bulk module enable/disable, IP filtering) are reserved for sponsors. Starkiller has no CLI of its own — every action is a browser action against the API — so the examples below are the shell-side minimum to raise and reach the UI; see the Empire note for the server side.

No corpus evidence — derived from upstream documentation (Starkiller and Empire repositories, Empire `config.yaml` defaults).

## Typical use cases

### Open the Starkiller UI from a running Empire server

Raise the GUI that ships inside Empire.

```
./ps-empire server
```

Outcome: Empire starts the REST API and serves Starkiller on the same port; browse to https://localhost:1337 and log in with the Empire admin credentials created on first boot (defaults `empireadmin` / `password123`, defined under `database.defaults` in `empire/server/config.yaml`).

### Move the UI (and API) to another port

Serve both on a non-default port.

```
# empire/server/config.yaml
api:
  ip: 0.0.0.0
  port: 8443
```

Outcome: on the next start Empire binds both the REST API and Starkiller to 0.0.0.0:8443 — the `api.ip`, `api.port` and `api.secure` keys govern the API and the UI together, so the GUI moves wherever the API moves.
