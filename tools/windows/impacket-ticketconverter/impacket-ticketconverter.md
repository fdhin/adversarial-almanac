---
name: "impacket-ticketconverter"
categories:
  - corpus-windows
homepage: "https://github.com/fortra/impacket"
origin: "corpus"
github-repo: "fortra/impacket"
packaged-by: []
meta-checked: "2026-11-09"
github-commits-year: 169
gh-activity-checked: "2026-11-09"
worth: 6.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# impacket-ticketconverter

> Convert Kerberos tickets between MIT ccache and Windows kirbi (KRB-CRED) formats.

## What it's for

ticketConverter.py takes two positional arguments — an input ticket file and an output filename — and converts between the MIT krb5 ccache format used by impacket's `-k` tools, klist and Linux Kerberos, and the kirbi (KRB-CRED) format produced and consumed by mimikatz, Rubeus and kekeo. The direction is picked from the file extensions, so the same command shape works both ways; the only option is `-b`/`--base64` to decode a base64-wrapped input ticket. Typical flow: pull a `.kirbi` out of a Windows-side tool and turn it into a ccache for impacket, or the reverse to import a Linux-side ticket into Rubeus. It converts existing tickets only — it neither requests nor forges them.

## Typical use cases

### kirbi to ccache — make a Windows-exported ticket usable by impacket

```
impacket-ticketconverter user.kirbi user.ccache
```

Outcome: reads the KRB-CRED file and writes `user.ccache`; export `KRB5CCNAME=user.ccache` and impacket scripts run with `-k -no-pass` now authenticate as that ticket's user.

### ccache to kirbi — make an impacket ticket usable by Rubeus/mimikatz

```
impacket-ticketconverter user.ccache user.kirbi
```

Outcome: writes a .kirbi that can be imported on a Windows host, e.g. `Rubeus.exe ptt /ticket:user.kirbi` or `mimikatz # kerberos::ptt user.kirbi`, giving that session the ticket's identity.
