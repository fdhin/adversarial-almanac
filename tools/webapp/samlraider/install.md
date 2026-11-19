---
tool: "samlraider"
build-system: "java"
upstream: "https://github.com/SAMLRaider/SAMLRaider"
recipe: "upstream"
license: "MIT"
---

# Installing samlraider

> SAML Raider — Burp Suite extension for testing SAML infrastructures: message manipulation and X.509 certificate management.

Source: pentest-book feed; upstream [`SAMLRaider/SAMLRaider`](https://github.com/SAMLRaider/SAMLRaider) (the repo was transferred from `CompassSecurity/SAMLRaider`; the old URL redirects to the same repository). Extension page in the BApp Store: [SAML Raider](https://portswigger.net/bappstore/c61cfa893bb14db4b01775554f7b802e).

## From the BApp Store (recommended)

```
Burp: Extensions tab -> BApp Store -> SAML Raider -> Install
```

Outcome: the extension loads and the SAML Raider message editor and Certificates tab become available in Burp.

## Manual JAR load

```bash
# download a release JAR, e.g. the current release:
curl -LO https://github.com/SAMLRaider/SAMLRaider/releases/download/v2.5.2/saml-raider-2.5.2.jar
```

Then in Burp: `Extensions` tab → `Add` → choose the downloaded SAML Raider JAR file.

Outcome: the extension installs from the local JAR without the BApp Store; check the extension's output/errors pane for load problems, and update by repeating with a newer release JAR.

## Notes

- No distro packages this extension (checked 2026-11-13); it only runs inside Burp Suite.
- The repository's MIT license text lives in the repo's `LICENSE` file.

Backlinks: [[webapp]] · [[samlraider]]
