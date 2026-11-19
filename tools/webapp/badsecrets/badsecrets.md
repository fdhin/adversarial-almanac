---
name: "badsecrets"
categories:
  - blackarch-webapp
version: "1.3.0.r2.g5721731"
homepage: "https://github.com/blacklanternsecurity/badsecrets"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-10-31"
build-system: python
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "blacklanternsecurity/badsecrets"
github-stars: 837
github-pushed: "2026-11-06"
github-archived: false
github-issues: 8
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 318
gh-activity-checked: "2026-11-09"
---

# badsecrets

> Black Lantern Security's library and CLI for detecting known or weak cryptographic secrets — feed it a cookie, JWT, viewstate or signed URL and it tells you which framework produced it and whether the key is a publicly known one.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Many frameworks ship with (or get configured from tutorials with) default or leaked signing keys — ASP.NET machineKeys, Flask session password, Django SECRET_KEY, Rails secret_key_base, Laravel APP_KEY, Shiro AES keys, IBM LTPA keys, Telerik keys. badsecrets is both the curated database of those secrets and a language-agnostic recognition layer: it identifies what kind of cryptographic product you captured, tries it against every module's known-key list offline, and reports the recovered secret. Passive modules verify a product you already hold; active modules (URL mode) fingerprint a live target and forge products with known keys to confirm they're accepted — e.g. a Shiro rememberMe deserialization or a GlobalProtect default-master-key auth bypass. When no known secret matches, it emits ready-to-run hashcat commands for the weak-but-unknown cases. A Python library (`check_secret()`), a CLI, per-attack helper commands (`telerik-knownkey`, `symfony-knownkey`), a Docker image and a BBOT module all wrap the same engine. It does not capture tokens for you — bring the cookie/JWT/viewstate or a URL that sets them.

## Typical use cases

### Check a captured token against every module

```
badsecrets eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIn0.dozjgNryP4J3jVmNHl0w5N65S9X7Y...
```

Outcome: the product type is auto-recognized (here a JWT) and checked against all modules; a hit prints the detecting module, the recovered secret and decoded details. On a miss, compatible hashcat cracking commands are suggested instead (skip them with `--no-hashcat`).

### Carve and check everything a page sets (URL mode)

```
badsecrets --url http://target.example.com/login.aspx
```

Outcome: fetches the URL, extracts every cryptographic product it can find (cookies, viewstate, tokens) and checks each against the module list; active modules additionally forge known-key products and send them to confirm acceptance. `--header 'Cookie: foo=bar'`, `--proxy`, `--user-agent` and `--timeout` shape the request; `-P`/`--passive-only` disables the active probes for pure sniffing.

### Identify an ASP.NET viewstate machine key

```
badsecrets 3RP87RgckNbfc7fNdaHzH9YLqbIhzpA64gFfWB49lhzHpuHFAAO7C7Dl2zh0dWUqobBb4hwiZJ1a2bhP77aQiwqvVqg= 9BD98A7D http://target.example.com/default.aspx
```

Outcome: checks the viewstate plus generator against the known machineKey lists — pass the page URL too, because .NET 4.5 derives the validation key from the page path, and a viewstate checked without its URL can report nothing even when the key is known. This is why `--url` mode often succeeds where a hand-pasted viewstate fails.

### Test your own (or leaked) key list

```
badsecrets eyJhbGciOiJIUzI1NiJ9... -c my_keys.txt
badsecrets --url http://target.example.com/ -c Shiro_RememberMe_Key:key1,key2
```

Outcome: `-c`/`--custom-secrets` adds a key file (or inline comma-separated keys) on top of the built-in lists; with a `MODULE:` prefix the keys are tested only against that module — the way to check a client-specific key dump against a specific technology. Files are capped at 100k.

### Hunt known Telerik and Symfony keys on a live target

```
telerik-knownkey --url http://target.example.com/Telerik.Web.UI.DialogHandler.aspx
symfony-knownkey --url http://target.example.com/
```

Outcome: dedicated helpers (pip-installed commands, or `python ./badsecrets/examples/*.py` from a source checkout) that brute-force the known-key sets against a live instance even without a sample token — telerik-knownkey identifies Telerik hash/encryption keys and can build working exploit URLs for patched (post-2017) Telerik versions, and symfony-knownkey detects Symfony `_fragment` secret keys when no signed URL is visible.

### Enumerate the module library

```
badsecrets -l
```

Outcome: lists every passive and active module with a one-line description — the quickest way to see which platforms' products are recognizable (viewstate variants, framework cookies, JWTs, LTPA, NextAuth, …) before scripting the library.
