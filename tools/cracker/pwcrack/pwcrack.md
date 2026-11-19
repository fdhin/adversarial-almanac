---
name: "pwcrack"
categories:
  - blackarch-cracker
version: "368.b6fba8a"
homepage: "https://github.com/L-codes/pwcrack-framework"
packaged-by: [blackarch]
blackarch-builddate: "2025-04-29"
build-system: ruby
license: GPL3
meta-checked: "2026-11-09"
github-repo: "L-codes/pwcrack-framework"
github-stars: 514
github-pushed: "2026-06-30"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pwcrack

> Ruby framework that fingerprints a password hash's algorithm and cracks it through online APIs, plugins and a local database.

## What it's for

pwcrack-framework (binary `pwcrack`) takes a ciphertext — usually a password hash, but also app-specific blobs like Cisco type-7, Navicat, FinalShell or FileZilla secrets — auto-detects the algorithm, and runs every matching plugin against it: 40 offline cracking interfaces plus 23 online lookup services, with 63 algorithms supported overall. It is the "paste the hash, get the plaintext" tool for unsalted/common hashes and for odd vendor formats that big crackers handle poorly, and it ships a local-database mode so lookup results are cached offline. It is not a GPU brute-forcer; heavily salted or strong hashes are out of its reach unless an online service knows them.

## Typical use cases

### Auto-detect and crack a hash

Paste a bare hash and let the framework figure out the algorithm and try every matching plugin.

```
pwcrack e10adc3949ba59abbe56e057f20f883e
```

Outcome: pwcrack prints the detected algorithm, then each plugin's result as it runs; for this MD5 of `123456` the plaintext `123456` is reported. Exit happens when a plaintext is found (or `-q` was given to stop early).

### Crack with the algorithm forced

Skip fingerprinting and restrict the run to one algorithm family.

```
pwcrack e10adc3949ba59abbe56e057f20f883e md5
```

Outcome: only plugins supporting MD5 are invoked — faster and quieter than the auto run, useful on odd ciphertexts that fingerprint as several algorithms.

### Crack a prefixed blob with one selected plugin through a proxy

Run a single known plugin against a base64-wrapped ciphertext, routing lookups through a proxy.

```
pwcrack -x http://198.51.100.10:3128 -s pmd5 base64:ZTEwYWRjMzk0OWJhNTlhYmJlNTZlMDU3ZjIwZjg4M2UK
```

Outcome: only the `pmd5` plugin is used, its HTTP requests going through the proxy; `-x` accepts `http://`, `https://` and `socks4/5://` URLs.

### Build and maintain the local password database

Seed an offline database so future runs resolve without network access.

```
pwcrack initdb my_dict.txt
pwcrack add spring2024 hunter2
pwcrack updatedb
```

Outcome: `initdb` hashes the given wordlist (or the bundled default) into the local database; `add` appends new plaintexts to the word list, and `updatedb` folds them into the database — after which the `localdb` plugin resolves those plaintexts instantly and offline.

### Interactively paste ciphertexts

Start a read-eval loop that keeps asking for ciphertexts without re-launching.

```
pwcrack gets
```

Outcome: prompts `Cipher Text >>` repeatedly; each entered value goes through the same analysis-and-crack path, and an empty line exits.

*No corpus evidence — derived from upstream documentation.*
