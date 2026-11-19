---
name: "impacket-wmiexec"
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

# impacket-wmiexec

> Semi-interactive shell over WMI/DCOM (TCP 135 + dynamic RPC ports) — remote execution without a service binary.

## What it's for

impacket-wmiexec gives a cmd-style shell on a Windows host by launching commands through WMI over DCOM. Unlike psexec-style tools it drops no service binary: commands are started via WMI and their output is written to a temp file that is read back over SMB (the ADMIN$ share by default, change with `-share`; `-nooutput` skips retrieval entirely). Reach for it with local-admin credentials (password, NTLM hash or Kerberos ticket) when you want a quieter psexec alternative — it still needs SMB for output, and authentication still leaves the usual WMI logs.

## Typical use cases

### Shell with plaintext credentials

```
impacket-wmiexec example.com/administrator:Password1@198.51.100.10
```

Outcome: semi-interactive cmd prompt on the target as the given user, each command's output echoed back; type `exit` to quit.

### Pass-the-hash shell

```
impacket-wmiexec example.com/administrator@198.51.100.10 -hashes :5f4dcc3b5aa765d61d8327deb882cf99
```

Outcome: the same shell authenticated with an NTLM hash instead of a password — the standard move after dumping hashes (e.g. with secretsdump) when no plaintext is available.

### Kerberos-authenticated shell (ticket, no password)

```
KRB5CCNAME=admin.ccache impacket-wmiexec -k -no-pass example.com/administrator@target.example.com -dc-ip 198.51.100.10
```

Outcome: shell authenticated from the ccache ticket instead of NTLM — the pattern after obtaining a TGT or when NTLM is restricted; address the target by FQDN so the ticket's SPN matches.

*Commands verified against the current impacket source (`examples/wmiexec.py`).*
