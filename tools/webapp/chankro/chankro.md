---
name: "chankro"
categories:
  - blackarch-webapp
version: "21.7b6e844"
homepage: "https://github.com/TarlogicSecurity/Chankro"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "TarlogicSecurity/Chankro"
github-stars: 501
github-pushed: "2023-09-05"
github-archived: false
github-issues: 8
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# chankro

> Generates a PHP page that runs a script or binary outside PHP's disable_functions/open_basedir cage.

## What it's for

Chankro (TarlogicSecurity) builds a PHP file that bypasses `disable_functions` and `open_basedir` restrictions. The trick: PHP on Linux invokes the `sendmail` binary when `mail()` runs, and if `putenv()` is allowed the crafted page sets `LD_PRELOAD` to a small shared object dropped by the payload — that .so executes your real payload (a bash script such as a reverse shell, or a custom binary like a meterpreter) as a child of sendmail, completely outside PHP's function restrictions. You need write access to a web-reachable path (e.g. through an upload or an LFI-with-write), plus `mail()` and `putenv()` being available. The generator itself is a Python 2 script run on your own machine; only the output .php is placed on the target.

## Typical use cases

### Generate a payload for a bash reverse shell

Wrap a shell script into a PHP dropper.

```
chankro --arch 64 --input rev.sh --output chan.php --path /var/www/html
```

Outcome: writes `chan.php` embedding (base64) both `rev.sh` and the matching-architecture .so dropper; `--arch` must match the target's PHP process architecture (64/32) and `--path` is the absolute web-reachable directory where the .so will be dropped on execution. Upload `chan.php` into that path and request it once — when the page calls `mail()`, the preloaded object runs `rev.sh`, and your listener receives the shell regardless of `disable_functions`/`open_basedir`.

### Deliver a custom binary instead of a script

Use the same delivery for a prebuilt executable.

```
chankro --arch 64 --input payload.bin --output chan.php --path /var/www/html
```

Outcome: same mechanism, but the embedded payload is a compiled binary (for example a meterpreter or a statically-linked implant); the .so loader executes it directly, which fits post-exploitation tooling that cannot be expressed as a shell script.
