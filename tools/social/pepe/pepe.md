---
name: "pepe"
categories:
  - blackarch-social
version: "13.b81889b"
homepage: "https://github.com/woj-ciech/pepe"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "woj-ciech/pepe"
github-stars: 39
github-pushed: "2021-06-01"
github-archived: true
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pepe

> Collect information about email addresses from Pastebin.

## What it's for

PEPE (Post Exploitation Pastebin Emails) parses Pastebin
`email:password` dumps and enriches every leaked address through free and
API-based lookups: Google, Trumail (deliverability), Pipl and FullContact
(identity and social profiles), and HaveIBeenPwned (breach history). It
can optionally notify the leak victim by email that their password was
exposed, and push every record into Elasticsearch for exploration. It
accepts exactly one input format — lines of `email:password`; anything
else is not parsed. The archived upstream requires API keys (Pipl,
FullContact, optionally Pushsafer) and Gmail credentials to be filled into
its JSON config file before use. This is post-breach credential-lead
triage, not a dump search engine: the dump file (or a live Pastebin
stream) is the input, not a query.

## Typical use cases

### Triage a dump interactively

Walk a leaked-credential file one address at a time, choosing which lookup
to run per record.

```
python pepe.py --file dump.txt --interactive --whitelist
```

Outcome: Prints each `email:password` pair found in the file and offers a
per-record menu (Google search, HaveIBeenPwned, Pipl, FullContact,
deliverability test, notify victim); `--whitelist`/`--blacklist` controls
which mail domains are processed.

### Enrich a whole dump non-interactively

Batch-run selected lookups over every record in the file.

```
python pepe.py --file dump.txt --modules hibp google fullcontact trumail --elasticsearch --blacklist
```

Outcome: Runs the chosen modules against each leaked address, printing
breach names, social-profile links and identity data per record, and
indexes everything into the Elasticsearch instance from the config file.

### Monitor Pastebin live

Process new pastes as they appear instead of a static file.

```
python pepe.py --stream
```

Outcome: Streams recent Pastebin content, extracts `email:password` pairs
as they appear, and feeds them into the configured enrichment modules.

*No corpus evidence — derived from upstream documentation.*
