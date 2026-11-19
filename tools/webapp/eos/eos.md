---
name: "eos"
categories:
  - blackarch-webapp
version: "16.47f0086"
homepage: "https://github.com/synacktiv/eos"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: python
license: custom:THE BEER-WARE
meta-checked: "2026-11-09"
github-repo: "synacktiv/eos"
github-stars: 364
github-pushed: "2024-10-18"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# eos

> Enemies Of Symfony — debug mode Symfony looter.

## What it's for

EOS (Enemies Of Symfony) is Synacktiv's looter for Symfony applications left in debug mode: it abuses the exposed web profiler (`/_profiler`) to pull general info, the phpinfo page's environment variables (APP_SECRET, DATABASE_URL and friends), registered routes, credentials captured in POST request logs, project files located via a wordlist, and the application's source code. Reach for it the moment a target shows a Symfony debug toolbar or an accessible profiler — the profiler warns "never enable in production" precisely because EOS-style looting follows. It exploits no vulnerability itself: it only reads what a misconfigured profiler already exposes, and it can also craft long-lived "remember me" cookies once the APP_SECRET is known.

## Typical use cases

### Full loot of a debug-mode Symfony target

The canonical sweep.

```
eos scan http://target.example.com --output results
```

Outcome: prints Symfony/PHP versions and environment, credentials found in POST request logs, Symfony variables from phpinfo, project files found by wordlist, routes and source file listings, then saves all retrieved files under `results/` — one directory holding the application's configuration, database and sources.

### Scan with custom headers

Reach the profiler behind a session or specific client fingerprint.

```
eos scan -H "Cookie: foo=bar" -H "User-Agent: EOS" http://target.example.com
```

Outcome: performs the same full scan with every supplied header (`-H` repeatable) applied to each request, letting the profiler pages be fetched as an authenticated or otherwise filtered client.

### Download one known file

Pull a specific file when you already know the path.

```
eos get http://target.example.com config/services.yaml
```

Outcome: retrieves exactly that project file from the target via the profiler and writes it locally — the quick way to grab `.env`-equivalents or a service configuration without a full scan.

### Forge a remember-me cookie

Turn a leaked APP_SECRET into a persistent session.

```
eos cookies -u user -H '$2y$13$<bcrypt-hash-of-user-password>' -s <app-secret>
```

Outcome: crafts a Symfony "remember me" cookie with a long lifetime for the given user from the password hash and the application secret previously extracted by `scan` — valid replay material against the target's remember-me handling.

No corpus evidence — derived from upstream documentation (README usage and sample output).
