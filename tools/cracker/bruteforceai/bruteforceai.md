---
name: "bruteforceai"
categories:
  - cracker
version: "git (2026-07-17)"
homepage: "https://github.com/mordavid/bruteforceai"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-09"
github-repo: "mordavid/bruteforceai"
github-stars: 1929
github-pushed: "2026-07-17"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
worth: 5.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 1
---

# bruteforceai

> Two-stage web-login attacker: an LLM finds the login form's selectors, then Playwright drives the credential attack.

## What it's for

BruteForceAI automates password testing against HTML login pages. Stage one (`analyze`) loads each target URL in headless Chromium and asks a large language model — a local Ollama model or a cloud Groq model — to identify the username, password and submit selectors, retrying with its own error feedback when a selector fails. Stage two (`attack`) replays username and password files through the discovered selectors with multithreading, human-like delays and jitter, User-Agent rotation and optional proxying, judging success by DOM changes after submission and logging every attempt to an SQLite database. It supports classic brute force (all combinations) and password spray (each password against every user), and can notify a Discord, Slack, Teams or Telegram webhook on success. Scope limits: HTML forms only — no SSH, HTTP Basic or API authentication — and the license is non-commercial.

## Typical use cases

### Identify login form selectors with a local LLM

Let the model discover the form structure for each target.

```
python3 BruteForceAI.py analyze --urls urls.txt --llm-provider ollama --llm-model llama3.2:3b
```

Outcome: each URL in urls.txt (one per line, e.g. `http://target.example.com/login`) is loaded headlessly and analyzed; the discovered selectors are stored in the `bruteforce.db` SQLite database for the attack stage, and already-analyzed URLs are skipped unless `--force-reanalyze` is given.

### Run a credential attack against the analyzed forms

Replay username/password lists through the discovered selectors.

```
python3 BruteForceAI.py attack --urls urls.txt --usernames users.txt --passwords passwords.txt --threads 10 --delay 5 --jitter 2 --success-exit
```

Outcome: headless Chromium fills and submits the form for each combination with randomized timing; a detected success reports the working credential pair, exits after the first hit, and every attempt is recorded in `bruteforce.db`.

### Password-spray a user list

Try each password against every account rather than every combination per account.

```
python3 BruteForceAI.py attack --urls urls.txt --usernames users.txt --passwords passwords.txt --mode passwordspray --threads 3
```

Outcome: passwords are cycled across all usernames with synchronized per-user delays, the spray pattern lockout policies tolerate better than full-combination attacks.

### Debug form analysis on a stubborn page

Watch the browser while the LLM works.

```
python3 BruteForceAI.py analyze --urls urls.txt --show-browser --browser-wait 5 --debug --llm-provider ollama
```

Outcome: the Chromium window is shown with a 5-second pause per page and debug output on the terminal, making selector misfires visible.

*No corpus evidence — derived from upstream documentation.*
