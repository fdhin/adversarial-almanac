---
name: "impacket-smbexec"
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

# impacket-smbexec

> Semi-interactive shell via a temporary service — command execution over SMB without uploading any binary to ADMIN$.

## What it's for

impacket-smbexec is impacket's implementation of the classic smbexec technique: it creates a temporary service on the target through the service control manager whose command line runs your command and redirects its output to a temp file, which is then read back over SMB. Unlike psexec.py it uploads nothing — no service binary ever lands in ADMIN$ — and output is retrieved via the C$ share by default (`-share` to change, `-mode SERVER` to instead open a listener on your side). It needs local-admin credentials (password, NTLM hash or Kerberos ticket) and speaks SMB on port 445 or 139. Expect service-creation events on the target: it is quieter than psexec, not silent.

## Typical use cases

### Pass-the-hash shell

```
impacket-smbexec example.com/administrator@198.51.100.10 -hashes :5f4dcc3b5aa765d61d8327deb882cf99
```

Outcome: semi-interactive cmd prompt on the target; each command runs through the temporary service and its output is echoed back, with no binary dropped on disk. Type `exit` to quit.

### Kerberos-authenticated shell through a pivot

```
KRB5CCNAME=admin.ccache proxychains4 -q impacket-smbexec -k -no-pass example.com/administrator@target.example.com
```

Outcome: the same shell authenticated with the ccache ticket (address the target by FQDN so the ticket matches), tunneled through a SOCKS pivot — the pattern for reaching internal hosts with no direct route, e.g. over a relayed/proxied session.

*Commands verified against the current impacket source (`examples/smbexec.py`).*
