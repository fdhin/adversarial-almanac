---
name: "faraday"
categories:
  - reporting
version: "5.25.0-0kali1"
homepage: "https://faradaysec.com"
origin: "kali"
recipe: "kali-dsc"
packaged-by: [kali, nixpkgs]
build-system: python
meta-checked: "2026-11-09"
github-repo: "infobyte/faraday"
github-stars: 6842
github-pushed: "2026-10-29"
github-archived: false
github-issues: 16
github-match: "search"
gh-checked: "2026-11-07"
worth: 8.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 688
gh-activity-checked: "2026-11-09"
---

# faraday

> Faraday — collaborative vulnerability-management server: ingest scanner findings, normalize them, track them in a multi-user web UI.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Faraday (Infobyte / FaradaySec) is an open-source vulnerability manager: a server that stores hosts, services, and vulnerabilities in PostgreSQL, normalizes output from 80+ tools through its plugin system (console plugins process a tool run, report plugins import artifacts like XML/JSON), and presents everything in a multi-user web interface for triage, deduplication, and reporting. Scope per the packaged version (Kali `faraday` 5.25.0): the package ships the server side — `faraday-server`, `faraday-manage`, and service wrappers. The old desktop client is discontinued and absent from this version; the user-facing client is the web UI the server exposes on port 5985, and the terminal client `faraday-cli` is a separate package/project. It requires a PostgreSQL server; it is a platform for managing findings, not a scanner itself.

## Typical use cases

### First start on Kali

Bring the whole stack up with one wrapper.

```
sudo faraday-start
```

Outcome: starts PostgreSQL, runs `faraday-manage initdb` automatically on first use, and launches the server, which serves the web client at `http://localhost:5985` (`/usr/bin/faraday` is a symlink to this wrapper; `sudo systemctl start faraday-server` is the service-manager equivalent).

### Initialize or repair the database

Prepare the PostgreSQL schema before serving.

```
faraday-manage initdb
```

Outcome: creates/initializes the Faraday database (prompting for credentials as needed) — run this once before starting the server from a non-wrapper context, e.g. under systemd.

### Run the server in the foreground with explicit settings

Serve with a chosen bind address and port, watching logs directly.

```
faraday-server --bind_address 127.0.0.1 --port 5985
```

Outcome: the API and web client are served on the given address/port (overriding `server.ini`); `--debug` adds debug logging, and `--with-workers` also starts the background job workers in the same process.

### Create the first login

Provision an initial user for the web UI.

```
faraday-manage create-superuser --username user --email user@example.com
```

Outcome: prompts for a password (hidden, with confirmation), then creates the superuser account used to log in at `http://localhost:5985`.

### Inspect available import plugins

Check which tool outputs Faraday can consume.

```
faraday-manage list-plugins
```

Outcome: lists the report/console plugins available to this installation, confirming whether a given scanner's output can be imported directly or needs conversion first.
