---
name: "spraycharles"
categories:
  - blackarch-cracker
version: "v2.0.2.r3.g131f8c6"
homepage: "https://github.com/Tw1sm/spraycharles"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-31"
build-system: python
license: BSD-3-Clause
meta-checked: "2026-11-09"
github-repo: "Tw1sm/spraycharles"
github-stars: 225
github-pushed: "2026-01-30"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# spraycharles

> Low-and-slow password sprayer that rotates passwords on an interval over days, with pluggable target modules and results analysis.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Password spraying designed to run unattended for the length of a long
engagement: `-a/--attempts` sets how many logins each user gets per interval
and `-i/--interval` the minutes between intervals, so a spray can quietly
rotate one password at a time across a user list over several days. Target
support is modular — Office365, OWA, EWS, Okta, ADFS, Cisco SSL VPN, Citrix
NetScaler, SonicWall, NTLM over HTTP, and SMB — and new services are easy to
template. It is deliberately single-threaded and volume-over-velocity ("low
and slow"): for fireprox-rotated or faster cloud-focused spraying its own
README points to tools like CredMaster. Extras: per-request jitter, a
pause-on-likely-success flag, Slack/Teams/Discord notifications, a YAML
config-file mode (each run also saves its options to `last-config.yaml`), and
an `analyze` command that statistically flags successful logins in the output.
Logs and results land in `~/.spraycharles` by default. Invoked as
`spraycharles` or `sc`.

## Typical use cases

### Run a multi-day low-and-slow spray against Office 365

Set it up once, check on it periodically.

```
spraycharles spray -u users.txt -p Password1 -m Office365 -a 1 -i 60 -o results.json
```

Outcome: sprays the single password `Password1` once per user, waits 60
minutes, then advances to the next password from the list (`-p` also accepts
a password-file path); attempts log to the console and `~/.spraycharles`,
results accumulate in `results.json`, and the username/password files can be
edited mid-run with changes taking effect at the next rotation.

### Spray SMB with a domain prefix

Target a Windows domain's file services directly.

```
spraycharles spray -u users.txt -p passwords.txt -m SMB -H 198.51.100.10 -d CORP
```

Outcome: authenticates to the host over SMB with `CORP\` prefixed to every
username; for SMB the analyzer simply reports entries whose NTSTATUS codes
indicate successful logins.

### Spray through a fireprox interface

Rotate the apparent source address against lockout-by-IP defenses.

```
spraycharles spray -u users.txt -p passwords.txt -m Office365 -f https://abc123.execute-api.us-east-1.amazonaws.com/fireproxy/
```

Outcome: routes every authentication attempt through the given fireprox AWS
API Gateway endpoint so each request originates from a different address,
which defeats simple source-IP lockout counters.

### Analyze results for likely successes

Statistically separate hits from noise after (or during) a spray.

```
spraycharles analyze results.json
```

Outcome: reads the output JSON, flags response lengths that are statistical
outliers compared to the failed-attempt baseline (and NTSTATUS successes for
SMB), and lists the credentials worth verifying by hand; `--analyze` on the
spray command runs the same analysis automatically after every interval.

### Harvest the internal domain name from NTLM endpoints

Recon the target before choosing spray options.

```
spraycharles parse https://example.com/ews
spraycharles parse smb://target.example.com
```

Outcome: sends an unauthenticated NTLM challenge to the HTTP or SMB endpoint
and prints the internal NetBIOS/AD domain name from the challenge response —
the value to feed `-d` when spraying.
