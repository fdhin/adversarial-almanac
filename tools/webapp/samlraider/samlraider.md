---
name: "samlraider"
categories:
  - webapp
homepage: "https://github.com/SAMLRaider/SAMLRaider"
origin: "pentest-book"
recipe: "upstream"
packaged-by: []
build-system: java
github-repo: "SAMLRaider/SAMLRaider"
github-stars: 453
github-match: "exact"
gh-checked: "2026-11-13"
worth: 7.0
worth-basis: "full (activity+popularity+recency+adoption)"
license: MIT
---

# samlraider

> Burp Suite extension for attacking SAML single sign-on: message manipulation, signature attacks, and X.509 certificate management.

## What it's for

SAML Raider (originally by Roland Bischofberger and Emanuel Duss, now maintained under the SAMLRaider org; associated with Compass Security) is the standard Burp extension for testing SAML 2.0 infrastructures. It has two halves. The SAML message editor hooks Burp's message views: SAMLResponse/SAMLRequest assertions show up decoded in the proxy and Repeater, where you can edit them, remove signatures (signature exclusion), re-sign messages and assertions with any key you control (signature spoofing), run eight common XSW (XML Signature Wrapping) attacks with one click, and inject XXE or XSLT payloads into the XML. The certificate manager imports, exports, clones and self-signs X.509 certificates and chains (PEM/DER), and manages private keys (PKCS#8 DER and traditional RSA PEM) — the toolkit you need to forge a trusted-looking assertion when the IdP/SP validation is weak. It understands the SAML Web Browser SSO and WSS SAML Token profiles over POST, Redirect, SOAP and URI bindings, plus custom SAML parameter names. It does not break cryptography — a correctly implemented SP still rejects forged tokens; it finds implementations that don't.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Intercept SAML traffic comfortably

Make the proxy stop exactly on SAML messages.

```
Burp: Proxy -> Options -> Intercept rules based on parameter name -> SAMLResponse
```

Outcome: Burp holds requests carrying a `SAMLResponse` parameter so you can forward them to Repeater; the SAML Raider message editor renders the base64-deflate decoded XML inline for editing. Custom parameter names (non-default SAML fields) are configured in the SAML Raider Certificates tab.

### Run XSW attacks against an assertion

Test whether signature validation can be wrapped around.

```
Repeater: select the SAML message -> SAML Raider tab -> SAML Attacks -> pick one of the 8 XSW variants -> Send
```

Outcome: the extension rewrites the assertion into the chosen signature-wrapping shape (original signature kept, attacker-modified copy inserted) and shows whether the server accepts it; the SAML Message Info pane displays the parsed assertion's references so you can reason about which variant applies.

### Spoof or strip a signature

Take control of assertion signing.

```
SAML Raider Certificates tab: import your private key + clone the target cert, self-sign the clone
Repeater: SAML Raider -> Self-Sign / remove signature -> Send
```

Outcome: with the target's certificate cloned and re-signed by your key, the message editor can re-sign the assertion as the "issuer" (signature spoofing) or drop the signature entirely (signature exclusion) — an SP that accepts the result is vulnerable (the historically successful class: nevisAuth CVE-2015-5372, Slack's 2017 bypass, FusionAuth CVE-2020-12676).

### Fire XXE/XSLT payloads at the SAML parser

Attack the XML processing itself.

```
Repeater: SAML Raider tab -> insert XXE / XSLT payload; enable raw mode first so the extension does not re-parse the message
```

Outcome: the payload travels verbatim (raw mode stops SAML Raider from re-serializing the XML, which would break the entity), letting you test for external-entity and stylesheet-processing bugs in the SAML stack (e.g. FusionAuth CVE-2021-27736).
