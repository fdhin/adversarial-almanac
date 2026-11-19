---
name: "gpp-decrypt"
categories:
  - blackarch-crypto
version: "v1.1.r4.g86ae8b7"
homepage: "https://github.com/t0thkr1s/gpp-decrypt"
packaged-by: [blackarch, kali]
blackarch-builddate: "2026-10-25"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "t0thkr1s/gpp-decrypt"
github-stars: 221
github-pushed: "2026-10-11"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# gpp-decrypt

> Decrypts Group Policy Preferences `cpassword` values — account credentials stored in GPP XML files on a domain's SYSVOL share — with the public Microsoft AES-256 key.

## What it's for

Windows Group Policy Preferences (introduced with Server 2008) let administrators deploy account passwords through Group Policy, encrypting them with an AES-256 key Microsoft later published; MS14-025 (2014) blocked storing new credentials this way, but leftover Groups/Services/Scheduled-Task XML files carrying `cpassword` attributes are still found in many domains, readable by any domain user under `\\<domain>\SYSVOL\<domain>\Policies\...\Preferences\Groups\Groups.xml`. This tool decrypts them in two mutually exclusive modes: parse a whole GPP XML file and print every credential, or decrypt a single pasted `cpassword` string. It also works as a Python library (`decrypt_password`, `parse_xml_file` from `gpp_decrypt`).

## Typical use cases

### Decrypt every credential in a GPP XML file

```
gpp-decrypt -f groups.xml
```

Outcome: parses the XML, reports how many credentials it found, and prints each entry's type (user account or group policy), username, and plaintext password — the standard flow once a Groups.xml has been retrieved from a domain controller's SYSVOL share.

### Decrypt a single cpassword string

```
gpp-decrypt -c "j1Uyj3Vx8TY9LtLZil2uAuZkFQA/4latT76ZwgdHdhw"
```

Outcome: decrypts the pasted `cpassword` blob directly and prints `Decrypted password: <plaintext>` — for when only the attribute value is at hand, not the whole XML file. `-f` and `-c` are mutually exclusive and one of them is required.

### Script-friendly output

```
gpp-decrypt -f groups.xml --no-banner --verbose
```

Outcome: suppresses the ASCII-art banner so output parses cleanly in scripts, while `--verbose` additionally shows a truncated form of each encrypted `cpassword`. There is no short quiet flag — banner control is `--no-banner`, and `-v` prints the version rather than verbosity.
