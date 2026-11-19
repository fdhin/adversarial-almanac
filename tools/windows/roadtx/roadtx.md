---
name: "roadtx"
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

# roadtx

> ROADtools Token eXchange: acquire, exchange and use every flavor of Azure AD / Entra ID token, including device registration and Primary Refresh Tokens.

## What it's for

roadtx (ROADtools Token eXchange, by Dirk-jan Mollema `_dirkjan`) is the
token Swiss-army knife of the ROADtools family. It implements a large
set of Entra ID authentication flows — username/password, device code,
refresh tokens, interactive Selenium logins, FIDO2/passkeys, Windows
Hello keys, federated (SAML) identities, Keepass-stored credentials —
and, crucially, the device-side flows: registering or joining devices,
requesting and renewing Primary Refresh Tokens (PRTs), enriching them
with MFA claims, and using a PRT to obtain access tokens the way the
Windows token broker (WAM) does. Tokens are kept in `.roadtools_auth`
and can be re-targeted to other resources or clients, decoded, or used
for raw graph requests. Built on the [[roadlib]] library; see also
[[roadrecon]] and [[roadoidc]]. Requires Python 3.10+.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Get a token with username and password

Acquire and cache a token with the simplest flow.

```
roadtx gettokens -u user@example.com -p Password1
```

Outcome: authenticates to the default Azure AD graph resource and saves
access and refresh tokens in `.roadtools_auth`; `-r`/`-c` retarget the
resource or client, and `--device-code`/`--as-app` switch to other
flows.

### Switch an existing token to another resource

Reuse the cached refresh token for a different API.

```
roadtx refreshtokento -r https://graph.microsoft.com
```

Outcome: redeems the refresh token from `.roadtools_auth` for a token to
`https://graph.microsoft.com` and updates the token file — no new
credentials needed.

### Register a device in Entra ID

Create a device identity that can hold a PRT.

```
roadtx device -a register
```

Outcome: registers a new device using the token already in
`.roadtools_auth`, writing its certificate and key as `.pem`/`.key` files
(named after the device, default `DESKTOP-<RANDOM>`); `-a join` performs
a full join instead.

### Request a Primary Refresh Token

Turn the device identity into a PRT.

```
roadtx prt -c device.pem -k device.key
```

Outcome: requests a PRT for the registered device and stores it in
`roadtx.prt`; renew it with `-a renew`, and use `roadtx prtauth` to
exchange it for access tokens like the Windows broker does.

### Decode what is in the token file

Inspect claims before using a token.

```
roadtx describe -f .roadtools_auth
```

Outcome: decodes the stored token and prints its header and claims (aud,
upn, roles, ...); `-t <token>` describes a pasted token instead, `-v`
adds extra detail.
