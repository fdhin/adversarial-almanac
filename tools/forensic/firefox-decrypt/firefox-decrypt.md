---
name: "firefox-decrypt"
categories:
  - blackarch-forensic
version: "1.1.3.r4.g1740363"
homepage: "https://github.com/unode/firefox_decrypt"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-03"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "unode/firefox_decrypt"
github-stars: 2512
github-pushed: "2026-09-28"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 25
gh-activity-checked: "2026-11-09"
---

# firefox-decrypt

> Extracts saved passwords from Mozilla Firefox, Waterfox, Thunderbird and SeaMonkey profiles, including Master-Password-protected ones.

## What it's for

firefox_decrypt dumps the credential store (logins.json / signons.sqlite plus key4.db) of Mozilla-based browser and mail profiles in readable form. If the profile has a Master Password, the tool decrypts as long as that password is known — it is a decryptor, not a cracker, and simply fails when the Master Password is unknown or wrong. It decodes through NSS (libnss3), which it locates by heuristics or via the `NSS_LIB_PATH` environment variable, and runs on Linux, macOS and Windows wherever a compatible NSS library is present. Typical use is recovering your own saved credentials after a profile backup, or triaging a captured profile image during authorized forensic analysis.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### List the profiles found on the machine

```
python3 firefox_decrypt.py --list
```

Outcome: prints a numbered list of discovered profiles (e.g. `1 -> xxxxxxxx.default-release`); the numbers are what `--choice` expects in non-interactive runs.

### Interactive decryption of a profile

```
python3 firefox_decrypt.py
```

Outcome: shows the numbered profile picker, prompts for the Master Password only if one is set (plain Return if none), then prints every saved credential as `Website / Username / Password` records.

### Non-interactive decryption for scripting

```
echo "$PASSWORD" | python3 firefox_decrypt.py --no-interactive --choice 2
```

Outcome: bypasses all prompts — profile number given by `--choice`, Master Password read from stdin — and writes the decrypted credentials to stdout, so the command can run unattended inside a larger recovery script.

### Export credentials in a structured format

```
python3 firefox_decrypt.py --format json > logins.json
```

Outcome: emits the same records as JSON (`--format` also accepts `human`, `csv`, `tabular` and `pass`; csv/tabular offer `--csv-delimiter`/`--csv-quotechar` control) for downstream processing.

### Decrypt a profile directory that is not in the default location

```
python3 firefox_decrypt.py /path/to/folder/containing/profiles.ini/
```

Outcome: reads profiles from the given directory (a mounted backup or copied profile tree) instead of the local default path, with the same interactive flow.
