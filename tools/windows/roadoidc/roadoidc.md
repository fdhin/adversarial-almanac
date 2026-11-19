---
name: "roadoidc"
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

# roadoidc

> Minimal OpenID Connect provider you host yourself, to abuse federated credentials (and optional MFA) in Entra ID.

## What it's for

roadoidc is the OIDC component of the ROADtools family by Dirk-jan
Mollema (`_dirkjan`): a small Flask application plus a config generator
(`genconfig.py`) that together act as a minimal OpenID Connect Provider
under your control. The point is federated-credential abuse: you run the
fake IdP at an HTTPS URL (Azure App Service or Blob Storage are the
documented hosts), register that issuer on an Entra ID app registration
or managed identity as a federated credential, and the provider will
issue the tokens that [[roadtx]] then exchanges for real Entra ID access
— a persistence technique documented in the author's release blog. It
can also run as an External Authentication Method that auto-approves any
MFA request, intended strictly for labs. It ships as scripts inside the
ROADtools repository (no separate installable package); siblings in the
vault: [[roadlib]], [[roadrecon]], [[roadtx]].

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Generate the IdP certificate and configuration

Create the signing key pair and Flask config for your issuer URL.

```
python genconfig.py -i https://yourappname.azurewebsites.net
```

Outcome: writes the IdP certificate and private key (`roadoidc.pem`,
`roadoidc.key`) and a Flask config (`flaskapp/app_config.py`) bound to
that issuer — the issuer must be the exact base URL where the config
will be hosted; add `-k` to set a fixed key ID.

### Host the provider as an Azure App Service

Stand the Flask app up at the issuer URL.

```
cd flaskapp/ && az webapp up -n yourappname --sku B1 --runtime PYTHON:3.10
```

Outcome: deploys roadoidc so that
`https://yourappname.azurewebsites.net/.well-known/openid-configuration`
serves the discovery document — verify that endpoint before pointing any
federated credential at it.

### Render static config files for static hosting

Produce the discovery and keys documents for blob storage.

```
cd flaskapp/ && flask run
wget http://127.0.0.1:5000/.well-known/openid-configuration -O config.json
wget http://127.0.0.1:5000/keys.json -O keys.json
```

Outcome: runs the app locally and downloads the two static JSON
documents; upload them to the hosting container (issuer URL must match)
and the IdP is live without keeping a server running.

### Run it as an auto-approving MFA External Authentication Method

Lab-only: fake MFA through an Entra ID External Authentication Method.

```
python genconfig.py --eam -i https://yourappname.azurewebsites.net
```

Outcome: generates a configuration with the EAM endpoints enabled; after
registering it in Entra ID (Security → Authentication methods → Add
external method, discovery endpoint `<issuer>/eam/.well-known/
openid-configuration`), any MFA prompt scoped to it is approved without
interaction.
