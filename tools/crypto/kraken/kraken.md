---
name: "kraken"
categories:
  - blackarch-crypto
version: "32.368a837"
homepage: "http://opensource.srlabs.de/projects/a51-decrypt"
packaged-by: [blackarch]
blackarch-builddate: "2014-06-19"
build-system: other
license: GPL3
meta-checked: "2026-11-09"
github-repo: "joswr1ght/kraken"
github-stars: 62
github-pushed: "2014-10-22"
github-archived: false
github-issues: 5
github-match: "search"
gh-checked: "2026-11-09"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# kraken

> SRLabs' A5/1 cracking utility that breaks GSM 2G encryption with precomputed rainbow tables.

## What it's for

Kraken is the A5/1 decryption utility from Security Research Labs, developed by Frank A. Stevenson in C++ with Python components, running on CPUs or AMD GPUs. It implements a time/memory tradeoff attack against the A5/1 stream cipher used to encrypt GSM 2G calls and SMS: community-computed rainbow tables (distributed via BitTorrent) reverse the mapping from the cipher's 64-bit internal state to the first 64 bits of keystream. Keystream samples are typically obtained by capturing GSM bursts with a software-radio setup — historically Airprobe with gnuradio, later replaced for most users by Osmocom phones — after which table lookups recover the session key. Note the namesakes: this is the SRLabs GSM tool, not the cryptocurrency exchange and not the same-named LUKS/Docker projects. The BlackArch package builds a single `kraken` binary, but the upstream git server no longer allows anonymous access and the surviving project wiki documents only the attack theory, so no verifiable command-line examples exist to publish.

*No corpus evidence — derived from upstream documentation.*

*GitHub: `joswr1ght/kraken` — a community fork of the SRLabs source tree (Kraken/TableConvert/a5_cpu, "Copyright 2009. Frank A. Stevenson"); the original `git.srlabs.de` upstream is not anonymously reachable.*
