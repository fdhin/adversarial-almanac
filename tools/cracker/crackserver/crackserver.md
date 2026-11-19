---
name: "crackserver"
categories:
  - blackarch-cracker
version: "33.e5763ab"
homepage: "https://github.com/averagesecurityguy/crack"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
github-repo: "averagesecurityguy/crack"
github-stars: 34
github-pushed: "2015-01-20"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# crackserver

> XML-RPC client/server pair (crackserver/crackclient) that runs configured hash-cracking jobs on a remote host, plus a standalone local mode.

## What it's for

Stephen Haywood's `crack` toolkit splits password cracking into a server that owns the cracking tools and a lightweight client that can run anywhere on the network. `crackserver` exposes an XML-RPC endpoint with `crack` and `results` methods; `crackclient` uploads a hash file plus a hash type, receives a job id, and polls the server until the job completes, printing recovered passwords as they arrive. The actual engines are defined in a config file that maps hash types (`pwdump-lm`, `pwdump-nt`, `ike`, `dcc`, Metasploit-style `john_netntlm*` files) to command lines for John the Ripper, rcracki_mt, ike-scan psk-crack or hashcat, with `{file}`/`{output}` placeholders and multiple commands per type tried in order. It is Python 2-era software.

## Typical use cases

### Start the cracking server

Launch the XML-RPC listener on the host that has the cracking tools installed.

```
crackserver -l 0.0.0.0 -p 8000
```

Outcome: creates `config/crackserver.cfg` and `config/crack.cfg` from defaults on first run, constructs the CrackManager from the config, and serves the `crack` and `results` methods on the chosen address, printing a confirmation line for each stage.

### Submit a hash file from a client

Push cracking work to the remote host.

```
crackclient hashes.txt pwdump-nt -s 198.51.100.10 -p 8000
```

Outcome: uploads the file to the server, receives a job id, polls roughly every 10 seconds, and streams each recovered result line to stdout until the job reports complete.

### Run the same crack locally

Skip the network round-trip when working on the cracking host itself.

```
crack-standalone hashes.txt pwdump-nt
```

Outcome: executes the configured command chain for the `pwdump-nt` type against the local file and prints recovered passwords directly, using the same core modules as the server.

*No corpus evidence — derived from upstream documentation.*
