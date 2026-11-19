---
name: "impacket-psexec"
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

# impacket-psexec

> Semi-interactive SYSTEM shell on Windows: uploads a service binary over SMB, installs it as a service, pipes cmd.exe back.

## What it's for

Kali packaging of `psexec.py` from fortra/impacket — the classic
PsExec-style remote-execution variant. It authenticates over SMB
(password, NT hash, or Kerberos), enumerates shares and picks the first
writable one (with local-admin rights that is ADMIN$), uploads its
embedded RemCom service binary there, creates and starts a service
pointing at it, and pipes a semi-interactive `cmd.exe` back over a named
pipe. It requires local-administrator privileges on the target and is
the noisiest of impacket's execution family — it leaves a service
install, an uploaded binary, and Windows event log entries — where
impacket-smbexec, -wmiexec, -dcomexec and -atexec trade features for
footprint.

## Typical use cases

### Pass-the-hash to a SYSTEM shell

```
impacket-psexec -hashes :NTHASH example.com/administrator@198.51.100.10
```

Outcome: uploads the RemCom binary to ADMIN$ (first writable share),
starts the temporary service and drops a semi-interactive cmd prompt as
`nt authority\system` (`C:\Windows\system32>`); `exit` tears the shell
down and removes the service. The same command shape works with a
plaintext password instead of `-hashes`, and with a bare
`user@198.51.100.10` target when no domain is involved.

### Authenticate over Kerberos

```
impacket-psexec -k -hashes :NTHASH example.com/administrator@dc.example.com
```

Outcome: `-k` performs the SMB session setup with Kerberos (ticket from
`KRB5CCNAME`, falling back to the supplied secret to obtain one), which
keeps the execution NTLM-free on hosts where SMB signing or NTLM is
restricted; the target must be the FQDN hostname of the machine.
