---
name: "tweetshell"
categories:
  - blackarch-cracker
version: "21.47a415c"
homepage: "https://github.com/thelinuxchoice/tweetshell"
packaged-by: [blackarch]
blackarch-builddate: "2020-04-11"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "thelinuxchoice/tweetshell"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# tweetshell

> Interactive multi-threaded Twitter password brute-forcer in bash, routed through Tor.

## What it's for

TweetShell is a shell script that runs a menu-driven, multi-threaded password brute force against a single Twitter account, testing on the order of 400 passwords per minute across up to 20 parallel curl workers. All traffic is routed through a local Tor SOCKS proxy so successive attempts come from different exit addresses, which is how it works around simple login-rate limiting. It can save a session and resume it on a later run, verifies that the target username exists before attacking, and ships a default wordlist of roughly 39,000 eight-letter candidates. Only use it against accounts you own or have explicit written permission to test.

## Typical use cases

### Brute-force a Twitter account's password over Tor

Start the Tor daemon, then run the packaged menu script as root.

```
sudo systemctl start tor
sudo tweetshell
```

Outcome: prints a legal disclaimer and interactive prompts for the target username, the password list (a default list is installed at `/usr/share/tweetshell/passwords.lst`), and the thread count, then starts testing candidates through Tor while showing live per-thread progress; on success the working password is displayed and the session can be saved for resuming later.

*No corpus evidence — derived from upstream documentation.*
