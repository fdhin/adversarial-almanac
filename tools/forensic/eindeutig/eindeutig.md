---
name: "eindeutig"
categories:
  - blackarch-forensic
version: "20050628_1"
homepage: "https://www.jonesdykstra.com/"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-14"
build-system: make
license: GPL-1.0-or-later
meta-checked: "2026-11-09"
github-repo: "aur-archive/eindeutig"
github-stars: 0
github-pushed: "2015-08-15"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# eindeutig

> Parse Outlook Express DBX mail stores: folder trees, message tables, and per-message dumps (installs as dbxparse).

## What it's for

Eindeutig (Keith J. Jones, 2005; a descendant of dbxparse) reads
Outlook Express `.dbx` files: the per-folder message stores and the
master `Folders.dbx` that indexes them. It recognizes which kind of
file it has from the DBX magic, then prints either the profile's folder
hierarchy or a delimiter-separated table of messages (message id,
timestamp, sender and recipient names/addresses, subject, account) and
can dump each message's raw content to numbered `.txt` files. The
BlackArch package builds the original 2005 source and installs the
binary as `dbxparse`, because that is the program name in the tool's
own usage text. It is a triage tool for a mail format that ended with
the Windows XP era; for modern Windows mail stores (PST/OST) use other
tooling.

## Typical use cases

### Map the folder tree of a profile

Start with the master file to see what folders exist.

```
dbxparse Folders.dbx
```

Outcome: the folder hierarchy stored in the master `Folders.dbx` is
printed as a nested tree, telling you which per-folder `.dbx` files to
examine next.

### Produce a spreadsheet-ready message table

List every message in one folder without dumping bodies.

```
dbxparse -s -t , inbox.dbx
```

Outcome: a header row plus one row per message with comma separators
(default is TAB) — import the output straight into a spreadsheet for
review; `-s` suppresses the per-message file dumps.

### Dump all messages of a folder to files

Export message content for closer inspection.

```
dbxparse -e -o export inbox.dbx
```

Outcome: the message table is printed and each message's raw content is
written as a zero-padded numbered file (`000001.txt`, `000002.txt`, ...)
into `export/` (default is the current directory).

### Dump a single message under a new number

Extract one message and control its output name.

```
dbxparse -e -o export -i 5 -r 1 inbox.dbx
```

Outcome: only message 5 from the table is dumped, written as
`000001.txt` — `-i` selects the message index and `-r` renumbers the
output, handy when assembling exhibits.

### Force the file type

Override autodetection when the header byte is ambiguous.

```
dbxparse -f suspect.dbx
```

Outcome: the file is treated as a folders-type store regardless of its
type byte; `-e` forces email-type the same way, and combining `-e`
with `-f` is rejected.

*No corpus evidence — derived from upstream documentation.*
