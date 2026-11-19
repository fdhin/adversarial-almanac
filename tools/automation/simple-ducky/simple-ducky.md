---
name: "simple-ducky"
categories:
  - blackarch-automation
version: "20.f15079e"
homepage: "https://code.google.com/p/simple-ducky-payload-generator"
packaged-by: [blackarch]
blackarch-builddate: "2019-08-15"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "gitbrew/simple-ducky"
github-stars: 14
github-pushed: "2023-08-22"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# simple-ducky

> Menu-driven payload generator for the USB Rubber Ducky.

## What it's for

simple-ducky builds USB Rubber Ducky attack packages without writing Ducky
Script by hand. From a numbered menu it assembles the payload, encodes the
injection script to `inject.bin`, drops the evil executable into a local web
directory for staging, and can start a matching Meterpreter or Ncat
listener. It is an early-2010s project: payloads and the bundled duck
encoder 2.6 target the Windows Vista/7/8 era, so treat it as a teaching and
CTF-lab tool rather than a modern red-team asset. It stages payloads you
select — it does not exploit anything on its own.

## Typical use cases

### Generate a complete Rubber Ducky attack from the menu

Launch the interactive generator and let it build every piece of the attack.

```
simple-ducky
```

Outcome: a numbered menu opens; picking a payload (reverse shell, hash
dump, phishing/web-attack subsets, custom builder) produces the
`inject.bin` for the Ducky, places the payload executable in the web
directory, and optionally launches the listener. Run the dependency-checker
menu entry first if components are missing.

### Encode a Ducky Script into inject.bin

Turn a hand-written Ducky Script into the binary the Rubber Ducky executes,
optionally for a non-US keyboard layout.

```
java -jar duckencode.jar -i script.txt -o inject.bin -l fr
```

Outcome: `inject.bin` is written from `script.txt`; `-l` accepts us, uk,
fr, pt or a path to a custom layout `.properties` file. Copy the file to
the Ducky's storage and it executes on insertion.

*No corpus evidence — derived from upstream documentation.*
