---
name: "rshack"
categories:
  - blackarch-crypto
version: "64.cf197e3"
homepage: "https://github.com/zweisamkeit/RSHack"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "zweisamkeit/RSHack"
github-stars: 97
github-pushed: "2018-11-19"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rshack

> Menu-driven RSA workbench bundling six classic attacks with key extraction, construction and encipher/decipher utilities.

## What it's for

RSHack collects the classic hand-calculable RSA attacks — Wiener, Håstad, Fermat, Bleichenbacher, common modulus, and chosen plaintext — behind one interactive menu, alongside key utilities: extracting the parameters of a public or private PEM key, constructing PEM keys from raw parameters, and enciphering or deciphering messages. Everything is driven through the menu: you pick an attack or tool by its menu entry and it prompts for that module's argparse-style arguments (for example Fermat asks for `-n modulus -e public_exponent`, private key construction for `-p p -q q -e exponent [-o file]`, deciphering for `-n modulus -d exponent -c ciphertext`). It targets small, education-grade keys; there is no heavy factoring engine or scripting interface, and the per-module scripts have no standalone entry points, so the menu is the only way in.

## Typical use cases

### Run the interactive workbench

```
rshack
```

Outcome: prints the RSHack banner followed by the attack menu (1 Wiener, 2 Håstad, 3 Fermat, 4 Bleichenbacher, 5 common modulus, 6 chosen plaintext) and the tools menu (a public key parameter extraction, b private key parameter extraction, c private key construction, d public key construction, e decipher, f encipher); entering a choice prompts for that module's arguments and prints the result — for example choice 3 with `-n <modulus> -e 65537` attempts Fermat factorization and, when p and q are close, prints both factors and the private exponent, while choice c with `-p <prime1> -q <prime2> -e 65537 -o key.pem` writes the constructed PEM private key to `key.pem`.

*No corpus evidence — derived from upstream documentation.*
