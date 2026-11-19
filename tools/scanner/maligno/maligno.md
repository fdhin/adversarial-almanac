---
name: "maligno"
categories:
  - blackarch-scanner
version: "2.5"
homepage: "http://www.encripto.no/tools/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# maligno

> Metasploit payload server that dishes out AES-encrypted shellcode disguised as web content.

## What it's for

Maligno (Encripto) sits between Metasploit and the target: at startup it generates shellcode with msfvenom for every payload in its XML config, then serves it over HTTP(S) as innocuous-looking web responses, AES-encrypted and encoded (Base64, hex or binary) according to the configured profile. A companion generator builds clients that connect, decrypt and inject the payload — letting you simulate specific C&C traffic or a targeted malware delivery for adversary-replication and IDS/NIDS testing. Clients request payloads by ID, so one server can serve several payload types; the shellcode points at your pre-configured Metasploit multi/handler. Server and client behaviour are shaped through `server_config.xml` (documented in the bundled `server_config.explained`). Packaged commands: `maligno-srv` and `maligno-clientgen` (Python 2).

## Typical use cases

### Serve the configured payloads

Start the delivery server.

```
maligno-srv
```

Outcome: reads `server_config.xml` from the working directory, calls msfvenom to build the configured payloads, and starts the HTTP(S) listener; clients sending the right request parameter receive the encrypted shellcode. Start the Metasploit multi/handler first — the payload connects back to it.

### Generate a client for the target

Build the piece you actually deliver.

```
maligno-clientgen -f server_config.xml -s true
```

Outcome: writes a client script that speaks the exact request/response and decryption profile from the config file — `-s true` embeds the payload standalone; `-s false` keeps it dependent on the server. Deliver it to the target like a malware dropper.
