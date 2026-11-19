---
name: "facebot"
categories:
  - blackarch-recon
version: "23.57f6025"
homepage: "https://github.com/pun1sh3r/facebot"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "pun1sh3r/facebot"
github-stars: 57
github-pushed: "2014-09-21"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# facebot

> Master/client Facebook automation for social-engineering recon: scripted profiles, friend campaigns, and on-demand crawling of a target's profile.

## What it's for

facebot (pun1sh3r, 2014-era) is an automated Facebook profile and reconnaissance system split in two components: `masterbot.py` is the controller that listens on a port and dishes out instructions, and `facebotv2.py` is the client bot that operates a fake profile — populating its "about me" section, posting wall content (YouTube links, news, quotes) at random intervals, sending friend requests, and crawling friends-of-friends. In target mode it crawls all information from a given profile link. It predates Facebook's current API restrictions and expects a MySQL database for storing loot plus (for target mode) a Facebook app access token in the config file — much of this surface no longer functions against today's Facebook, so treat it as a historical social-engineering toolkit whose architecture (master/mini-bot with campaign and target modes) is the interesting part.

## Typical use cases

### Start the controller

Bring up the master that coordinates the bots.

```
python masterbot.py
```

Outcome: starts the instruction server, e.g. `Masterbot listening on: 127.0.0.1:1337`, ready to accept client bots (create the loot database first per the repo schema).

### Run a client bot in impersonation mode

Operate the fake profile so it looks alive.

```
python facebotv2.py -c config.cfg -B
```

Outcome: bot mode (`-B`) populates the profile from the config file, posts random wall content (videos, news, quotes) and sends friend requests to impersonate a real user; `-b` alone builds only the "about me" section.

### Run a friend campaign

Grow the profile's social graph automatically.

```
python facebotv2.py -c config.cfg -cm
```

Outcome: campaign mode (`-cm`) sends out friend requests and gathers friends-of-friends, expanding the network the bot can see and store to the database.

### Crawl a target profile

Harvest everything visible on a specific profile.

```
python facebotv2.py -c config.cfg -tm <target_profile_id>
```

Outcome: target mode crawls all information from the given profile id (requires a Facebook app access token set as `api_key` in the config), handing the collected data back for storage.

No corpus evidence — derived from upstream documentation.
