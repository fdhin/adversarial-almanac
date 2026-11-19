---
name: "anontwi"
categories:
  - blackarch-social
version: "1.1b"
homepage: "http://anontwi.sourceforge.net/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "epsylon/anontwi"
github-stars: 31
github-pushed: "2015-09-01"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# anontwi

> A Python 2 client for Twitter and GNU Social (Identi.ca) that routes API traffic through proxies and can encrypt tweets and direct messages with AES + HMAC-SHA1.

## What it's for

Anonymity-oriented social networking client from the early 2010s. It sits in front of the Twitter v1.1 / GNU Social APIs, sends every request with randomized HTTP headers through a configurable proxy (typically Privoxy into Tor), splits long messages automatically, and can post tweets and direct messages as AES+HMAC-SHA1-encrypted blobs that only holders of a shared PIN can read. Extras include timeline backup (up to 3200 messages), fake geolocation, a GTK+ interface, and an IRC "bot slave" mode for remote control. It targets OAuth endpoints of its era and runs on Python 2, so treat it as a historical tool for studying privacy techniques rather than a working client for today's platforms.

## Typical use cases

### Obtain OAuth tokens through the PIN flow

Register the client against your own account before first use.

```
anontwi --tokens
```

Outcome: prints an authorization URL to open while signed in; the PIN you receive is entered back at the prompt and AnonTwi displays the token key/token secret pair, which can be exported as environment variables so later commands don't need them inline.

### Generate a shared encryption PIN

```
anontwi --gen
```

Outcome: prints a strong base64 PIN key (for example `1Geh0RBm9Cfj82NNhuQyIFFHR8F7fI4q7+7d0a3FrAI=`). Share it with recipients out-of-band — it is the symmetric secret used by `--enc`.

### Publish an encrypted tweet

```
anontwi -m "Hello World" --enc --pin "1Geh0RBm9Cfj82NNhuQyIFFHR8F7fI4q7+7d0a3FrAI="
```

Outcome: posts the message as an encrypted blob to your timeline; readers holding the same PIN decrypt it with AnonTwi instead of seeing plaintext.

### Send a plain message routed through Tor

```
anontwi -m "Hello World" --proxy "http://127.0.0.1:8118"
```

Outcome: the API call leaves through the local Privoxy-to-Tor listener rather than your own IP, with randomized HTTP headers. The same `--proxy` flag combines with `--tokens`, `--short` and the other commands.

### Shorten a URL before posting

```
anontwi --short "https://example.com/very/long/path"
```

Outcome: prints a shortened URL suitable for pasting into a tweet or direct message.

*No corpus evidence — derived from upstream documentation.*
