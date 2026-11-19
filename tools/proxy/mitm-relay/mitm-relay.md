---
name: "mitm-relay"
categories:
  - blackarch-proxy
version: "41.2d168b2"
homepage: "https://github.com/jrmdev/mitm_relay"
packaged-by: [blackarch]
blackarch-builddate: "2026-03-14"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "jrmdev/mitm_relay"
github-stars: 634
github-pushed: "2026-03-10"
github-archived: false
github-issues: 7
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# mitm-relay

> Relays arbitrary TCP/UDP protocols — including SSL and STARTTLS — through an intercepting HTTP proxy such as Burp, by wrapping each client request and server response in HTTP POSTs; built for thick-client assessments.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Intercepting protocols your HTTP proxy cannot speak natively (IMAP, XMPP, MySQL, SMB, DNS, custom binary protocols): mitm-relay listens locally, forwards to the real server, and mirrors each direction of the conversation to your proxy as HTTP POSTs — requests appear under URLs containing `CLIENT_REQUEST`, replies under `SERVER_RESPONSE` — so you can read and edit raw protocol data with the match-and-replace rules you already know. Because the wrapping is asynchronous, servers that stream multiple responses are handled. A Python script hook (`handle_request`/`handle_response`) can transform traffic instead of or alongside the proxy — the right place to fix protocol length fields after an edit, which the tool deliberately does not do for you. Without a proxy configured it is a plain console dumper. Repeater/Intruder-style features are pointless here: they would target the dummy echo server, not the real session.

## Typical use cases

### Monitor a non-HTTP protocol

```
mitm-relay -r 143:target.example.com:143
```

Outcome: listens on local port 143 and relays to the destination; with no `-p` proxy both directions are dumped to stdout (monitoring mode). Point the client at the relay — via a hosts-file entry or a DNAT rule redirecting the destination traffic to the relay host — and watch the raw session.

### Intercept and edit the session through Burp

```
mitm-relay -r 4444:target.example.com:8443 -p 127.0.0.1:8080
```

Outcome: every client→server message arrives in Burp as a POST to a `CLIENT_REQUEST` URL and every server→client reply as a `SERVER_RESPONSE` POST, editable with match-and-replace; the relay format is `[udp:|tcp:]lport:rhost:rport` and `-r` may be repeated to run several relays in one process.

### Strip SSL/STARTTLS encryption

```
mitm-relay -r 143:target.example.com:143 -c server.pem -k server.key -p 127.0.0.1:8080
```

Outcome: the relay presents your certificate (`-c`/`-k`, also usable for client certificates via `-cc`/`-ck`) for TLS-wrapped and STARTTLS sessions, so plaintext reaches the proxy; generate the certificate with a CommonName matching what the client expects, or import your proxy's CA and sign it.

### Modify traffic with a script

```
mitm-relay -r 143:target.example.com:143 -s sample_script.py
```

Outcome: `handle_request()` and `handle_response()` in the script transform each message before it is forwarded (for example rewriting `COMPRESS=DEFLATE` to `COMPRESS=NONE` on an IMAP session, or updating length fields after edits); a working example ships as `sample_script.py` in `/usr/share/doc/mitm-relay`.
