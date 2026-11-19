---
name: "username-anarchy"
categories:
  - blackarch-automation
version: "72.e063191"
homepage: "http://www.morningstarsecurity.com/research/username-anarchy"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2025-04-29"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "urbanadventurer/username-anarchy"
github-stars: 1496
github-pushed: "2024-09-20"
github-archived: false
github-issues: 2
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# username-anarchy

> Generates every plausible username format (and email address) from a list of real names, for brute-force and spraying preparation.

## What it's for

username-anarchy (Ruby, by Andrew Horton / morningstarsecurity) turns "Anna Key" into `anna`, `akey`, `a.key`, `kanna`, `key.anna` and dozens of other format variants — because usernames are half the password-guessing problem. Feed it full names harvested from a website, document metadata, or LinkedIn-style "firstname + lastinitial" lists, and it emits a deduplicated username list targeted at the organization's naming convention, either in every known format or restricted to formats you select. This is the standard bridge between OSINT name collection and a credential attack: with a large username list you can spray a few weak passwords per account while staying under lockout thresholds. It also ships country name databases and a `--recognise` mode that identifies which format an observed username uses.

## Typical use cases

### Expand one known name into all username variants

```
./username-anarchy anna key
```

Outcome: prints every format-plugin variant for the name (anna, annakey, anna.key, akey, kanna, key.a, …) to stdout — use it when the target's naming convention is unknown.

### Generate usernames from a harvested name list in known formats

```
./username-anarchy --input-file fullnames.txt --select-format flast,lfirst,f.last > usernames.txt
```

Outcome: reads CSV/TAB/space-delimited names (with optional column headers such as `firstname,lastname`) and writes only the selected format variants to `usernames.txt`, ready for a spraying tool. This input-file + select-format pattern is the workhorse invocation.

### Generate every variant for a whole name list

```
./username-anarchy --input-file web_users.txt > users.txt
```

Outcome: applies all format plugins to every name in `web_users.txt` and writes the full username candidate list to `users.txt`.

### Identify the naming convention from one real username

```
./username-anarchy --recognise j.smith
```

Outcome: matches the observed username against the Facebook name dataset and reports the plugin in use (`f.last` here), telling you which `--select-format` value to use for the bulk generation.

### Produce email addresses instead of bare usernames

```
./username-anarchy --input-file fullnames.txt --select-format first.last -@ @example.com
```

Outcome: appends the suffix to every candidate, emitting `first.last@example.com`-style addresses usable directly against a mail or VPN portal.

### Define a custom naming scheme

```
./username-anarchy -F "v-%f%l" andrew horton
```

Outcome: applies a custom format string (`%f` firstname, `%l` lastname, `%i.f` first initial, `%D` digit) to the given names, producing `v-andrewhorton` — for conventions the built-in plugins don't cover.

### List the available format plugins

```
./username-anarchy --list-formats
```

Outcome: prints every format plugin name with an example (first, firstlast, first.last, flast, lfirst, FLast, first1, …) so you can pick exact `--select-format` values.
