---
name: "php-rfi-payload-decoder"
categories:
  - blackarch-cracker
version: "30.bd42caa"
homepage: "https://github.com/bwall/PHP-RFI-Payload-Decoder"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "bwall/PHP-RFI-Payload-Decoder"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# php-rfi-payload-decoder

> A small PHP web application (Brian Wallace, 2012) for decoding RFI bot payloads: hand it a payload URL or pasted source and it strips the encoding layers to expose the deobfuscated PHP and the bot's configuration.

*No corpus evidence — derived from the packaged source. The upstream GitHub repo is gone (frontmatter `github-status: gone`); mirrors of the project survive on GitHub (e.g. spnow/PHP-RFI-Payload-Decoder), and this note was verified against that source.*

## What it's for

Analyzing the PHP payloads that remote-file-inclusion (RFI) bots serve to vulnerable sites. Such payloads are typically wrapped in nested base64/gzinflate-style layers; this application does the unwrapping. It has two parts: `Decoder`, an interactive front-end where you paste source or give a payload URL — it fetches the payload, auto-decodes the layers, and renders the raw versus decoded source side by side with syntax highlighting, filing every submission on disk with origin and timestamp metadata — and `DecodingServer`, a collection endpoint that stores submitted dumps and serves them back for browsing. There is no command-line interface: it is a LAMP-era web app, so point any PHP-capable web server at the Decoder directory (the package installs both components under /usr/share/php-rfi-payload-decoder). The payload fetch happens server-side, so in a lab only submit URLs you are authorized to pull.

## Typical use cases

### Serve the decoder front-end locally

```
cd /usr/share/php-rfi-payload-decoder/Decoder && php -S 127.0.0.1:8080
```

Outcome: the front-end is reachable at http://127.0.0.1:8080/ — a form accepting pasted payload source or a payload URL, with the list of previously decoded payloads beneath it; each entry keeps the raw and the decoded text together.

### Submit a payload URL from the terminal

```
PAYLOAD=$(printf %s 'http://ATTACKER_IP/payload.txt' | base64 -w0)
curl -G http://127.0.0.1:8080/submit.php --data-urlencode "u=$PAYLOAD"
```

Outcome: submit.php base64-decodes the `u` parameter into the payload URL, the server fetches it (capped at 16 MB), strips the decoding layers, and stores the record under temp/ — the entry then appears in the index list, where clicking through shows the decoded source and its origin URL.
