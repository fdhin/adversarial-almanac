---
name: "token-reverser"
categories:
  - blackarch-misc
version: "10.cbb4528"
homepage: "https://github.com/dariusztytko/token-reverser"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "dariusztytko/token-reverser"
github-stars: 110
github-pushed: "2020-05-20"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# token-reverser

> Word-list generator for cracking security tokens built from predictable data.

*No corpus evidence — derived from upstream documentation.*

## What it's for

token-reverser (Dariusz Tytko, Python) builds word lists for cracking
security tokens — password-reset links, session tokens, API keys — that turn
out to be unsalted hashes of predictable material: user ID, first/last name,
email, birth date, phone, a Unix timestamp. Feed it every data chunk you
know about the account and it emits permutations of those chunks joined by
candidate separators; pipe the list into hashcat or john. If the token
cracks, you have also learned how the application mints its tokens. It only
generates candidate strings — the cracking itself is left to the cracking
tool.

## Typical use cases

### Build a word list from known account data

Test whether a reset token is a hash of the registrant's own data.

```
token-reverser --date "Tue, 10 Mar 2020 17:12:59 GMT" Foo Bar foo.bar@example.com 1985-05-23 202-555-0185 > words
```

Outcome: writes every permutation of the data chunks joined by each
candidate separator, plus the date's Unix timestamp and the previous second,
to `words` — run `hashcat -m 0 <token-hash> words` next; a crack reveals the
token's construction (e.g. `md5(user ID!name!email!timestamp)`).

### Cover token-creation time skew

The observed Date header rarely matches the server clock exactly.

```
token-reverser -d "Tue, 10 Mar 2020 17:12:59 GMT" -o 120 74824 foo.bar@example.com
```

Outcome: emits candidates for the parsed timestamp plus the 120 preceding
seconds instead of the single default predecessor — catches tokens minted
minutes before or after the timestamp you can observe.

### Restrict the separators tried

Shrink the candidate space when the delimiter is known or suspected.

```
token-reverser -s "!:" 74824 Foo Bar foo.bar@example.com
```

Outcome: joins chunk permutations only with `!` and `:` instead of the full
default punctuation/whitespace set, producing a far smaller list for the
cracker to chew through.
