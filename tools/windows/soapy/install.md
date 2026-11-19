---
tool: "soapy"
build-system: "python"
upstream: "https://github.com/logangoins/SOAPy"
recipe: "upstream"
license: "see repository"
---

# Installing soapy

> AD recon and post-exploitation through the Active Directory Web Services (ADWS) protocol, tunneled through SOCKS.

Source: hacktricks feed; upstream [`logangoins/SOAPy`](https://github.com/logangoins/SOAPy).

## Install via pipx (recommended)

```bash
pipx install git+https://github.com/logangoins/SOAPy
```

This installs the `SOAPy` command globally for the user. There is no PyPI
release — always install straight from the git repository.

## Operational prerequisites

- A route to TCP 9389 (ADWS) on a domain controller — directly or, by
  design, through a SOCKS5 proxy (`proxychains4 SOAPy ...`).
- For ticket-based auth, a Kerberos ccache (`KRB5CCNAME`) or credentials
  (`domain/user[:password]@target` connection string).

## Not yet packaged

No distro packages this tool (checked 2026-11-13).

Backlinks: [[windows]] · [[soapy]]
