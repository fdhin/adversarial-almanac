---
name: "tempomail"
categories:
  - blackarch-misc
version: "26.5600ec3"
homepage: "https://github.com/kavishgr/tempomail"
packaged-by: [blackarch]
blackarch-builddate: "2023-07-10"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "kavishgr/tempomail"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# tempomail

> Standalone terminal client that instantly creates a disposable 1secmail address and prints incoming mail live.

## What it's for

tempomail (kavishgr) is a single static Go binary that creates a temporary email address via the 1secmail API the moment it starts, displays it, and then watches the inbox — printing each incoming message as it arrives and saving it under a directory of your choice (default `/tmp/1secmails/`). It is built for one-shot registrations and verifications where you need to read a confirmation mail without exposing a real address. Quitting with Ctrl+C (or SIGTERM) deletes every message on the service. Caveats: the project is unmaintained ("no longer maintained" per its README, and the upstream repository has since disappeared), it only renders plain-text mail, and it works only as long as the third-party 1secmail service stays reachable — check that before relying on it.

## Typical use cases

### Get a disposable address and read the verification mail

Spin up a throwaway identity for a single sign-up.

```
tempomail
```

Outcome: a fresh address prints immediately; as mail arrives it is displayed live and each message is saved under `/tmp/1secmails/`. Exit with Ctrl+C — all messages are deleted on the service.

### Keep the mail somewhere specific

Direct the saved copies to a directory you control.

```
tempomail -path /tmp/verification-mail
```

Outcome: identical behavior, but every received message is written under the given directory instead of the default.

*No corpus evidence — derived from upstream documentation.*
