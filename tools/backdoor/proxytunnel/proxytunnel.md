---
name: "proxytunnel"
categories:
  - post-exploitation
  - web
version: "1.13.0-1"
homepage: "https://github.com/proxytunnel/proxytunnel"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-05-31"
build-system: make
license: GPL-2.0-only WITH cryptsetup-OpenSSL-exception, BSD-3-Clause
meta-checked: "2026-11-09"
github-repo: "proxytunnel/proxytunnel"
github-stars: 548
github-pushed: "2026-05-31"
github-archived: false
github-issues: 15
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 44
gh-activity-checked: "2026-11-09"
---

# proxytunnel

> Connects stdin/stdout to any host:port through an HTTP(S) proxy's CONNECT method — best known as an SSH `ProxyCommand` helper.

## What it's for

proxytunnel bridges its standard input and output to a destination host through an industry-standard HTTPS proxy by issuing a CONNECT request, which is how you get SSH (or any other stream protocol) out of a network whose only egress is a web proxy. It handles proxy authentication several ways — basic `user:pass` on the command line, a credentials file, environment variables, or NTLM for Microsoft-style proxies — and can TLS-encrypt any leg of the path (client to proxy, proxy to destination, or proxy to proxy when chaining a second remote proxy with `-r`). It can also inject arbitrary HTTP headers or override the Host header/SNI. It is a single-stream pipe, not a VPN or SOCKS proxy: one process carries one connection, and the tunnelled protocol's own encryption is left to that protocol.

## Typical use cases

### SSH out through an HTTPS proxy

One `~/.ssh/config` entry routes every later connection for that host via the proxy.

```
Host jump
    ProtocolKeepAlives 30
    ProxyCommand proxytunnel -E -p proxy.corp.example.com:8080 -P user -d target.example.com:443
```

Outcome: `ssh jump` opens its connection by asking proxy.corp.example.com:8080 to CONNECT to target.example.com:443, with `-E` TLS-wrapping the client-to-proxy leg; proxytunnel prompts for the proxy password unless it is supplied via `$PROXYPASS`.

### Expose a proxied destination as a local port

Standalone mode for tools that cannot use a `ProxyCommand`.

```
proxytunnel -a 127.0.0.1:2200 -p proxy.corp.example.com:8080 -P user:pass -d target.example.com:22
```

Outcome: proxytunnel listens on 127.0.0.1:2200 and bridges it through the proxy to target.example.com:22, so `ssh -p 2200 127.0.0.1` reaches the target's sshd.

### Chain two proxies

Cross a proxy you don't control by making it hop to a second one you do.

```
proxytunnel -p proxy.corp.example.com:8080 -P user1:pass1 -r second-proxy.example.com:8080 -R user2:pass2 -d target.example.com:22
```

Outcome: the first proxy CONNECTs to second-proxy.example.com:8080 (authenticated with `-R`), which then CONNECTs to the destination — the tunnel traverses both hops end to end.

### Authenticate to an NTLM proxy

```
proxytunnel -N -t CORP -P user:pass -p proxy.corp.example.com:8080 -d target.example.com:443
```

Outcome: negotiates NTLM authentication with the proxy instead of basic auth; `-t` forces the NT domain when autodetection (which usually fails) picks wrong.

### Keep credentials off the command line

```
printf 'proxy_user = user\nproxy_passwd = secret\n' > ~/.proxytunnel-auth
proxytunnel -p proxy.corp.example.com:8080 -F ~/.proxytunnel-auth -d target.example.com:443
```

Outcome: proxy credentials are read from the passfile (accepted fields: `proxy_user`, `proxy_passwd`, `remproxy_user`, `remproxy_passwd`, one `field = value` per line), so nothing secret appears in `ps` output or shell history.

*No corpus evidence — derived from upstream documentation.*
