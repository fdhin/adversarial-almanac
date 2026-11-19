---
name: "gopherus"
categories:
  - blackarch-webapp
version: "33.90a2fd5"
homepage: "https://github.com/tarunkant/Gopherus"
packaged-by: [blackarch, aur]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "tarunkant/Gopherus"
github-stars: 3433
github-pushed: "2023-04-18"
github-archived: false
github-issues: 12
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gopherus

> Interactive generator of gopher:// links that turn an SSRF into a direct conversation with internal services — often to RCE.

## What it's for

Gopherus builds gopher-format URLs for exploiting Server-Side Request Forgery: when an application fetches a URL you supply, a gopher link makes it speak a raw protocol to an internal service — MySQL, PostgreSQL, FastCGI, Memcached, Redis, Zabbix or SMTP — with arbitrary payload bytes, enabling everything from database dumps to file writes and reverse shells. It is an interactive generator, not a scanner: choose the service with `--exploit`, answer the prompts (username, file, command, attacker IP...), and it prints the ready gopher link in plain and URL-encoded form. It cannot find the SSRF for you — you still need a fetch-able injection point in the application.

## Typical use cases

### Poison memcached for PHP deserialization

When the app caches data in memcached and PHP unserializes on read, write a poisoned key over gopher.

```
gopherus --exploit phpmemcache
```

Outcome: prompts for the PHP code to execute, then prints a gopher link whose fetch makes memcached store a serialized PHP object under the default key — URL-encode it into the SSRF endpoint so the app unserializes the payload on its next cache read.

### Get a reverse shell through Redis

When the SSRF reaches an unauthenticated Redis instance, have Redis write a payload for you.

```
gopherus --exploit redis
```

Outcome: asks for reverse shell or PHP shell plus attacker IP and port, then emits the gopher link whose fetch issues the Redis commands that persist the payload (web shell or cron-based callback), giving execution on the server.

### Exploit an open FastCGI listener

When port 9000 answers and a PHP file exists on disk, speak FastCGI directly through the SSRF.

```
gopherus --exploit fastcgi
```

Outcome: prompts for a PHP file present on the victim (a default is offered) and the command to run, then prints the gopher link that issues the FastCGI request executing that code.

Corpus evidence: gopher payload-crafting against an SSRF, including a phpmemcache exploit generation.
