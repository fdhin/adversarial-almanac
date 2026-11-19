---
name: "proxychains"
categories:
  - corpus-proxy
homepage: "https://github.com/haad/proxychains"
origin: "corpus"
github-repo: "haad/proxychains"
packaged-by: [nixpkgs]
meta-checked: "2026-11-09"
github-stars: 7983
github-pushed: "2024-06-08"
github-archived: false
github-issues: 17
github-match: "exact"
gh-checked: "2026-11-09"
worth: 4.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# proxychains

> Force TCP traffic of any program through SOCKS4/5 or HTTP proxies — the standard way to push un-proxifiable tools through a SOCKS pivot.

## What it's for

proxychains (proxychains-ng; the binary is `proxychains4`, usually also symlinked as `proxychains`) preloads a library that hooks network-related libc functions in dynamically linked programs and routes their TCP connections through the SOCKS4/5 or HTTP proxies listed in its config. The dominant use: once a SOCKS endpoint exists on an internal network — an `ssh -D` dynamic forward, a chisel SOCKS listener, a Metasploit SOCKS server — proxychains lets any TCP client that has no native proxy support (nmap, netexec, impacket, mysql, Kerberos tooling, evil-winrm) reach hosts on the far side of the pivot. Limits: it only works on dynamically linked programs (and proxychains plus the target must share the same dynamic linker), UDP is not proxied, and DNS through the chain — the `proxy_dns` directive — is the most common source of breakage. `strict_chain` fails when any listed proxy is dead; `dynamic_chain` skips dead proxies and keeps going.

## Typical use cases

### Point the chain at a SOCKS pivot

Add the pivot endpoint to the proxy list in `/etc/proxychains.conf` (or `/etc/proxychains4.conf`):

```
strict_chain
proxy_dns

[ProxyList]
socks5  127.0.0.1 1080
```

Outcome: every `proxychains <cmd>` invocation now prints `[proxychains] config file found: ...`, preloads its library, and logs `strict chain ... 127.0.0.1:1080 ... <target>:<port> ... ok` for each connection. Swap `strict_chain` for `dynamic_chain` to tolerate dead proxies, and comment out `proxy_dns` (resolving names via `/etc/hosts` instead) when name resolution through the chain misbehaves.

### Run enumeration quietly against an explicit config

```
proxychains4 -q -f /etc/proxychains.conf netexec smb 198.51.100.10 -u user -p 'Password1'
```

Outcome: netexec's SMB checks reach the internal host through the chain, with proxychains' own banner suppressed (`-q` overrides the config's quiet setting) and the chain taken from exactly the file given by `-f` — the practical form when juggling several pivots, since without `-f` the config search order is `$PROXYCHAINS_CONF_FILE`, `./proxychains.conf`, `~/.proxychains/proxychains.conf`, then `/etc/proxychains.conf`. The same pattern carries netexec's other modes, e.g. `netexec ldap 198.51.100.10 ...`.

### Port-scan a host through the pivot

```
proxychains nmap -sT -Pn -n --top-ports 1000 198.51.100.10 -v
```

Outcome: a full TCP connect scan of the target's top 1000 ports with every probe routed through the chain. `-sT` is mandatory (SYN/half-open scans cannot be proxied), `-Pn` skips host discovery, `-n` avoids DNS through the chain. Each connection walks the chain serially, so scans are slow — budget minutes, not seconds.

### Confirm specific service ports

```
proxychains nmap -sT -Pn -n 198.51.100.10 -p 135,445,5985
```

Outcome: a near-instant check that the Windows RPC/SMB/WinRM ports are reachable through the pivot before pointing credential or shell tools at the host.

### Open a WinRM shell through the pivot

```
proxychains -q evil-winrm -i 198.51.100.10 -u user -p 'Password1'
```

Outcome: evil-winrm authenticates over WinRM (5985) via the chain and drops a PowerShell prompt on the internal host; the chain log shows `strict chain ... 127.0.0.1:1080 ... 198.51.100.10:5985 ... ok`.

### Do Kerberos through the pivot

```
proxychains kinit user
proxychains smbclient -K //fileserver.example.com/share -U user
```

Outcome: a TGT is obtained from the internal KDC through the chain, then smbclient authenticates with Kerberos (`-K`) instead of NTLM to enumerate or mount the share on the internal file server — both TCP sessions transparently routed through the pivot.

### SSH through an existing chain, or build the next hop

```
proxychains -q ssh -i id_rsa user@198.51.100.10
```

Outcome: the SSH session to the internal host is established through the configured proxies — useful when the target itself is only reachable via another pivot. The reverse pairing is equally common: `ssh -fN -D 1080 user@jump.example.com` opens the SOCKS5 endpoint that proxychains then feeds all other tools through; chisel's SOCKS mode and the Metasploit socks proxy module serve the same role.

### Drive console clients at internal services

```
proxychains mysql -h 198.51.100.10 -u root -p
```

Outcome: the MySQL client's connection is routed through the chain and a SQL prompt opens on the internal database. Any other console client that lacks proxy settings works the same way — `curl http://target.example.com/...`, `ftp`, `redis-cli` — which is exactly the gap proxychains exists to fill.

### Run an attack server through the pivot

```
proxychains -q ntlmrelayx.py -t ldaps://198.51.100.10 -smb2support
```

Outcome: the relay server binds on your host, but its outbound connections to the internal LDAPS endpoint (and its SMB2 capture service) go through the chain, so relayed authentication you capture lands on a host only the pivot can reach. The same wrapping applies to `impacket-secretsdump user@target.example.com -k -no-pass` and the other impacket entry points.

### Run AD CS attacks through the pivot

```
proxychains -q certipy find -u user@corp.example.com -p 'Password1' -dc-ip 198.51.100.10
```

Outcome: certipy enumerates certificate authorities and vulnerable templates via the chain; the follow-on `certipy req` and `certipy auth` steps (request a certificate, then authenticate with its PFX) run the same way through the pivot.
