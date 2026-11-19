---
name: "sees"
categories:
  - blackarch-social
version: "67.cd741aa"
homepage: "https://github.com/galkan/sees/"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
github-repo: "galkan/sees"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# sees

> Python 2 spear-phishing sender that mails company users from addresses on their own domain, randomising the envelope sender to slip past spam controls.

## What it's for

SEES (Social Engineering Email Sender) automates targeted spear-phishing for authorised social-engineering audits: it sends emails that appear in the victim's client as an internal address (for example a manager at the target's own domain) while the SMTP envelope uses randomly generated sender strings, so perimeter anti-spam is less likely to cluster and block the campaign. It drives an existing local mail server (postfix on 127.0.0.1:25), supports plain-text and HTML bodies plus multiple attachments, spaces sends with configurable random delays, and logs each delivery result. It does not clone websites or harvest credentials — it is purely the mailing stage. The upstream repository is gone (archived copies survive on the Wayback Machine); the packaged version runs on Python 2 only.

## Typical use cases

### Send a text/HTML phishing email to a prepared target list

```
./sees.py --text --config_file config/config.cfg --mail_user config/mail.user --html_file data/html.text -v
```

Outcome: after agreeing to the disclaimer prompt, SEES sends one email per `mail.user` line through the SMTP server named in the config; `-v` prints each message, and the run reports `[+] from -> to` per target. The victim's client shows the From address you chose while the SMTP Return-Path carries a random string under your sending domain.

### Send phishing emails with attachments

```
./sees.py --attach data/sample.pdf data/sample.docx --config_file config/config.cfg --mail_user config/mail.user --html_file data/attach.text -v
```

Outcome: same as the text run, but each delivered message carries the listed files as attachments; `--html_file` here points at the plain body text that accompanies them.

### Define the campaign targets and mail settings

Target list lines are `FromEmail:FromName:Subject:TargetEmail`; an `exit` line stops parsing. Config keys: `domain` under `[mail]`; `server` and `time` (a fixed delay or `1,3` for a random range in seconds) under `[smtp]`; `type` and `log_path` under `[log]`.

```
cat config/mail.user
ceo@example.com:Jane Doe:Q4 Compensation:user@example.com
exit
```

Outcome: the next `sees.py` run sends this single message from `ceo@example.com` to `user@example.com` with subject `Q4 Compensation`, waiting the configured delay before it.

### Review what was delivered

```
cat sees.log
```

Outcome: one line per delivery in `Time : from <=> to :: Result` form — the SMTP dialogue result (for example `sent (250 ... OK ...)`) tells you which targets' servers accepted the message.

*No corpus evidence — derived from upstream documentation.*
