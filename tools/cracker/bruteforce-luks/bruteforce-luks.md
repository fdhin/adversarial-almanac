---
name: "bruteforce-luks"
categories:
  - blackarch-cracker
version: "54.788d637"
homepage: "https://github.com/glv2/bruteforce-luks"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2024-03-02"
build-system: autotools
license: GPL3
meta-checked: "2026-11-09"
github-repo: "glv2/bruteforce-luks"
github-stars: 282
github-pushed: "2024-03-01"
github-archived: false
github-issues: 17
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bruteforce-luks

> Multithreaded dictionary and mask search for the password of a LUKS encrypted volume.

## What it's for

bruteforce-luks tries to recover the password of a LUKS-encrypted volume by attempting to unlock at least one of its key slots. It has two modes: exhaustive, generating candidates from a character set between a minimum and maximum length with optional known beginning and end, and dictionary, trying every line of a file. Runs are multithreaded, progress can be printed periodically with `-v` or on demand by sending USR1 to the process, and a state file makes long searches resumable. It is aimed at partially remembered passwords — an exhaustive search of an unknown strong passphrase is computationally out of reach, and the tool cannot help with a damaged or missing LUKS header.

## Typical use cases

### Attack a volume with a wordlist

Try every line of a password file against the volume's key slots.

```
bruteforce-luks -t 6 -f passwords.txt -v 30 /dev/sdd1
```

Outcome: six threads test the dictionary entries; a progress line is printed every 30 seconds; on success stdout shows `Password found: <password>`, and an exhausted search prints `Password not found` on stderr.

### Exhaustive search around a partially remembered password

Generate candidates that begin and end with known fragments.

```
bruteforce-luks -t 8 -l 5 -m 10 -b "W4l" -e "z" -s "0123456789abcdefghijklmnopqrstuvwxyz" /dev/sda2
```

Outcome: eight threads try every 5-to-10-character string that starts with `W4l`, ends with `z` and uses only the given character set, reporting the password when a key slot decrypts.

### Work from a header backup instead of the live device

Attack a copy of just the LUKS header, leaving the volume untouched.

```
sudo cryptsetup luksHeaderBackup --header-backup-file luks-header /dev/sda1
sudo chown $USER luks-header
bruteforce-luks -t 4 -f passwords.txt luks-header
```

Outcome: the password search runs entirely against the extracted header file (the key-slot material lives there), so a bug or interruption cannot damage the volume; success prints `Password found: <password>`.

### Resume an interrupted search

Save and later continue the position of a long run.

```
bruteforce-luks -t 6 -f passwords.txt -w state.txt /dev/sdd1
bruteforce-luks -t 6 -w state.txt /dev/sdd1
```

Outcome: the first invocation writes its position to state.txt about once a minute; after stopping it (Ctrl-C), the second resumes exactly where it left off instead of restarting the dictionary.

*No corpus evidence — derived from upstream documentation.*
