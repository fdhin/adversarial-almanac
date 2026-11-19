---
name: "facebash"
categories:
  - blackarch-social
version: "17.95c3c25"
homepage: "https://github.com/thelinuxchoice/facebash"
packaged-by: [blackarch]
blackarch-builddate: "2019-06-02"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "thelinuxchoice/facebash"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# facebash

> Shellscript Facebook password brute-forcer that routes attempts through TOR, with save/resume sessions and a bundled ~39k-entry password list.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Interactive bash script that runs a password wordlist against a single Facebook account, chaining requests through TOR so consecutive attempts arrive from different exit nodes. Sessions can be saved and resumed across runs, and it ships a default password list of roughly 39,000 entries. The practical limit is Facebook's own throttling — an account locks for an hour after twenty wrong passwords, capping the attack at about twenty attempts per hour — which makes the tool more a demonstration of why online brute force against Facebook is impractical than a workhorse. The upstream repository has been deleted; this note is grounded in the archived project README.

## Typical use cases

### Run a TOR-routed brute-force session

Start a wordlist attack against one account with TOR as the transport.

```
service tor start
sudo ./facebash.sh
```

Outcome: with the TOR daemon running, the script prompts for the target account and wordlist options and begins attempting passwords over the TOR circuit, logging progress to a resumable session file; expect the ~20 attempts/hour ceiling imposed by Facebook's lockout.
