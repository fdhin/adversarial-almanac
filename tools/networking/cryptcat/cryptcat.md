---
name: "cryptcat"
categories:
  - networking
version: "1.2.1"
homepage: "http://sourceforge.net/projects/cryptcat"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# cryptcat

> The classic netcat with a Twofish-encrypted pipe — same flags, but data in transit is encrypted with a shared key.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Cryptcat (from farm9, 2005 era) is the original l0pth netcat with a Twofish-CBC encryption layer (with ciphertext stealing) wrapped around the data channel. Everything else behaves like traditional netcat: listen with `-l`, pick a port with `-p`, execute a program with `-e`, UDP with `-u`. Use it for quick encrypted file moves, pipes, or shells between two hosts you control when a full SSH setup is overkill. Critical caveat: when no key is supplied with `-k`, both sides silently fall back to a hardcoded key (`metallica`), which defeats the entire point — always pass `-k` with your own passphrase. The project is dormant; treat it as a legacy utility, not a modern crypto product.

## Typical use cases

### Receive an encrypted file transfer

Act as the listener while the other side sends.

```
cryptcat -k 'Password1' -l -p 1234 > received.bin
```

Outcome: listens on TCP 1234; incoming bytes are decrypted with the shared key and written to received.bin. Without `-k` the hardcoded default key is used — anyone can read the stream.

### Send a file over the encrypted channel

Push a file to a waiting listener.

```
cryptcat -k 'Password1' 198.51.100.10 1234 < secrets.txt
```

Outcome: connects to 198.51.100.10:1234, encrypts secrets.txt with Twofish using the shared key, and streams it; the listener's output file ends up as a plaintext copy.

### Serve an encrypted remote shell

Get shell access without a plaintext channel.

```
cryptcat -k 'Password1' -l -p 1234 -e /bin/sh
```

Outcome: binds a shell to port 1234 served through the encrypted pipe; a client running `cryptcat -k 'Password1' <this host> 1234` lands in an interactive shell.
