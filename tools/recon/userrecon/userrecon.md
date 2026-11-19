---
name: "userrecon"
categories:
  - blackarch-recon
version: "10.3b56891"
homepage: "https://github.com/thelinuxchoice/userrecon"
packaged-by: [blackarch]
blackarch-builddate: "2019-02-10"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "thelinuxchoice/userrecon"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# userrecon

> Interactive Bash script that checks whether one username exists across 75+ social networks and prints the profile URLs.

## What it's for

userrecon (by @linux_choice) answers a single OSINT question well: where does this handle already exist? You give it one username, it iterates its built-in list of 75+ social networks and web platforms, and prints each site where an account with that name was found. Typical use is pivoting an identifier from one breach or profile to other platforms, or checking handle squatting before registration. It is deliberately simple: interactive prompt, Bash + curl, no database, no output files — results scroll to the terminal (copy them out yourself). The upstream repository was deleted along with the author's other repos, so the packaged script is frozen at its 2019 state: the site list ages, and checks fail as platforms change their response behavior — verify coverage against a current alternative before relying on the full 75+ claim. For a maintained, non-interactive equivalent with file input and export, see sherlock-style tools such as [[tookie-osint]].

## Typical use cases

### Check one username across all supported platforms

Run the interactive check for a handle.

```
userrecon
```

Outcome: the script prompts `Enter Username:`; typing a name (e.g. `user`) and pressing Enter starts the sweep, printing each platform where the account exists with its profile URL as it is found — the output is the found-account URL list on screen, ready to be recorded for the investigation.

*No corpus evidence — derived from upstream documentation.*
