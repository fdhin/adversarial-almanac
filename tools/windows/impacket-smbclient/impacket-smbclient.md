---
name: "impacket-smbclient"
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

# impacket-smbclient

> Impacket's interactive SMB client: enumerate shares, browse, and move files to and from a Windows host.

## What it's for

Kali packaging of `smbclient.py` from fortra/impacket — a Python SMB
client that drops into an interactive mini-shell (`Type help for list of
commands`). It accepts every impacket credential form: password, NT hash,
AES key, Kerberos ccache, or anonymous/guest sessions, which makes it the
go-to tool for file-level access to a Windows or Samba host (checking
share permissions, grabbing and dropping files) once any credential is
in hand. It moves files but does not execute anything on the target —
for remote execution use impacket-psexec, -wmiexec, -smbexec or -atexec.

## Typical use cases

### Connect with a username and password and browse

```
impacket-smbclient example.com/user:'Password1'@198.51.100.10
```

Outcome: opens the mini-shell. `shares` lists the target's share names
with comments; `use SHARE` switches into one (it auto-lists the root);
`ls`, `cd` and `cat file` navigate and read without downloading.

### Test anonymous or guest access

```
impacket-smbclient -no-pass guest@198.51.100.10
```

Outcome: attempts a null/guest session and drops into the shell if the
server allows it; `shares` then shows exactly which shares are reachable
without credentials. Useful first probe before spending any captured
credential.

### Pass-the-hash into SMB

```
impacket-smbclient example.com/user@198.51.100.10 -hashes :NTHASH
```

Outcome: authenticates over NTLM with the raw NT hash (empty LM part)
rather than a password, then works exactly like a password session —
browse shares, `get`/`put` files.

### Authenticate with Kerberos (pass-the-ticket)

```
KRB5CCNAME=user.ccache impacket-smbclient -k -no-pass example.com/user@dc.example.com
```

Outcome: `-k` switches to Kerberos, reading the ticket exported in
`KRB5CCNAME` (e.g. produced by impacket-gettgt); `-no-pass` suppresses
the password prompt. The target must be the DC/service FQDN hostname —
Kerberos does not address hosts by IP.

### Upload and download files

```
# shares
# use data
# put tool.exe
# get notes.txt
```

Outcome: `use` connects to the share, `put` uploads a local file into
the current remote path and `get` downloads a remote file to the local
directory (change it first with `lcd`). A failed `put` on an otherwise
readable share is a quick write-permission test; `mget`/`rget` pull
masked and recursive batches.
