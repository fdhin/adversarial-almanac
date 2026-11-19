---
name: "ntlmv1-multi"
categories:
  - blackarch-crypto
version: "115.c17f17d"
homepage: "https://github.com/evilmog/ntlmv1-multi"
packaged-by: [blackarch]
blackarch-builddate: "2026-06-29"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "evilmog/ntlmv1-multi"
github-stars: 676
github-pushed: "2026-06-26"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 17
gh-activity-checked: "2026-11-09"
---

# ntlmv1-multi

> Reformats captured NTLMv1 / NTLMv1-ESS / MSCHAPv2 challenge-response hashes into DES halves crackable with hashcat mode 14000.

## What it's for

NTLMv1 authentication derives its response from the NT hash using DES, and the
resulting 24-byte response splits into three ciphertexts (CT1, CT2, CT3) that
can each be attacked directly as DES keys with hashcat mode 14000 — cracking
two of them reveals almost the whole NT hash regardless of password length.
ntlmv1-multi (by evilmog, based on atom's hashcat work) automates that split:
feed it a challenge-response line in Responder format
(`user::host:LM:NT:challenge`), with or without ESS, and it prints the
components, the CT1/CT2 hashcat candidate lines, and — via `ct3_to_ntlm.bin`
from hashcat-utils — the final 4 hex characters of the NT hash. It handles
MSCHAPv2 and `$99$` blobs, and with a cracked potfile it can reassemble the
complete NT hash. On BlackArch the package installs the script as the
`ntlmv1` command; upstream it is `ntlmv1.py` / `ntlmv1_nextgen.py`.

## Typical use cases

### Split a captured NTLMv1 hash for hashcat cracking

Turn a Responder capture into mode 14000 candidates.

```
ntlmv1 --ntlmv1 'user::DUSTIN-5AA37877:76365E2D142B5612980C67D057EB9EFEEE5EF6EB6FF6E04D:727B4E35F947129EA52B9CDEDAE86934BB23EF89F50FC595:1122334455667788'
```

Outcome: prints hostname, username, challenge, LM and NT responses, then
CT1/CT2/CT3 and two crackable lines (`CT1:challenge` and `CT2:challenge`) to
feed to `hashcat -m 14000 -a 3 --hex-charset`.

### Get machine-readable output with the NT-hash tail computed

Script the split and capture the last 4 hex characters of the NT hash at the
same time.

```
ntlmv1 --ntlmv1 'user::DUSTIN-5AA37877:85D5BC2CE95161CD00000000000000000000000000000000:***:1122334455667788' --json
```

Outcome: a single JSON object with `user`, `domain`, `challenge`, `lmresp`,
`ntresp`, `ct3`, `pt3` (the computed NT-hash tail) and ready-made `hash1`/
`hash2` strings. The same input shape (`:***:` in the NT field) is what an
ESS-enabled capture looks like, and is parsed the same way.

### Point the tool at your hashcat and hashcat-utils trees

Have the output include copy-paste commands using your local tool paths.

```
ntlmv1 --ntlmv1 'user::HOST:76365E2D142B5612980C67D057EB9EFEEE5EF6EB6FF6E04D:727B4E35F947129EA52B9CDEDAE86934BB23EF89F50FC595:1122334455667788' --hashcat ~/git/hashcat --hcutils ~/git/hashcat-utils
```

Outcome: the standard split plus the exact `hashcat` and `ct3_to_ntlm.bin`
invocations written against those directories, so the crack workflow can be
pasted straight into a shell.

*No corpus evidence — derived from upstream documentation.*
