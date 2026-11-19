---
name: "roadlib"
categories:
  - blackarch-windows
version: "511.f7b0c47"
homepage: "https://github.com/dirkjanm/ROADtools"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-25"
build-system: windows
license: MIT
meta-checked: "2026-11-09"
github-repo: "dirkjanm/ROADtools"
github-stars: 2756
github-pushed: "2026-10-22"
github-archived: false
github-issues: 14
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 63
gh-activity-checked: "2026-11-09"
---

# roadlib

> Shared Python library of the ROADtools framework: Azure AD/Entra ID authentication and the ROADrecon database model.

## What it's for

ROADlib is the common component of the ROADtools family by Dirk-jan
Mollema (`_dirkjan`): it packages the reusable parts that the ROADrecon
recon tool and the roadtx token tool are built on. Two surfaces matter:
an `Authentication` class implementing the many Azure AD / Entra ID
authentication flows (password, device code, refresh tokens, PRT, FIDO,
Windows Hello, federated and interactive/Selenium flows — the same
options `roadrecon auth` and `roadtx gettokens` expose), and an
automatically generated SQLAlchemy database model of the Azure AD
internal API that tools can use to read or build ROADrecon data files.
It is a library, not a command-line tool: you reach for it when writing
your own Entra ID tooling in Python instead of re-implementing token
handling. It lives in the `roadtools` namespace (`from roadtools.roadlib
import ...`) and requires Python 3.10+. Sibling packages in the vault:
[[roadrecon]], [[roadtx]] and [[roadoidc]].

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Reuse ROADtools authentication in your own script

Import the same authentication machinery roadrecon and roadtx use.

```
python3 -c "from roadtools.roadlib.auth import Authentication; print(Authentication)"
```

Outcome: resolves the `roadtools.roadlib` namespace and prints the class
— proof the library surface your script will build on (`Authentication`,
`DeviceAuthentication`) is importable in the current environment.

### Give your tool the standard ROADtools auth options

Attach the shared authentication argparse block to your own CLI.

```
python3 -c "import argparse; from roadtools.roadlib.auth import Authentication; p = argparse.ArgumentParser(); Authentication().get_sub_argparse(p, for_rr=False); p.parse_args(['-h'])"
```

Outcome: prints the full option set (`-u`, `-p`, `-r`, `-c`, `--device-code`,
`--as-app`, ...) that `roadtx gettokens` accepts — exactly the block your
tool inherits, so its flags stay consistent with the rest of the family.
