---
name: "http-put"
categories:
  - blackarch-misc
version: "1.0"
homepage: ""
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: perl
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# http-put

> One-shot Perl client that uploads a local file to a web server with a raw HTTP PUT request.

*No corpus evidence — derived from upstream documentation.*

## What it's for

http-put is the BlackArch packaging of `put.pl` v1.0, a minimal Perl script by H.D. Moore distributed with the O'Reilly "Network Security Tools" book examples. It does exactly one thing: open a TCP connection (optionally wrapped in SSL via Net::SSLeay), send `PUT <remote-path> HTTP/1.1` carrying the contents of a local file, and print the server's raw response. Its niche is testing whether a web server accepts PUT uploads — a classic misconfiguration that turns a writable directory into arbitrary file placement. There is no retry, authentication, proxy or chunking logic; one invocation is one request, which is exactly its value for a quick yes/no answer.

## Typical use cases

### Upload a file to a PUT-enabled web server

Write a local file to a remote path over plain HTTP.

```
http-put -h target.example.com -r /uploads/shell.asp -f shell.asp
```

Outcome: prints `[results]` followed by the server's complete HTTP response. A `201 Created` or `204 No Content` status means the bytes now live at the remote path; `405 Method Not Allowed` means PUT is disabled.

### Upload over HTTPS

Test an SSL-enabled service without dropping to a full web client.

```
http-put -h target.example.com -x -r /uploads/cmd.aspx -f cmd.aspx
```

Outcome: connects over SSL on the implied port 443 and prints the raw HTTPS response. The script requires the Net::SSLeay Perl module for `-x` and refuses to run without it rather than silently downgrading to plaintext.

### Hit a non-standard port and inspect the exact request

Point at another port and dump the request as sent.

```
http-put -h 198.51.100.10 -p 8080 -r /uploads/loot.txt -f loot.txt -v
```

Outcome: prints an `[Options Table]` and the full `[request]` (request line, Host header, Content-Length, body) to stderr before the `[results]` response — the way to confirm exactly what hit the wire when a server behaves unexpectedly.
