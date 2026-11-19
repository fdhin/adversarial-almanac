---
name: "apollo"
categories:
  - backdoor
homepage: "https://github.com/MythicAgents/Apollo"
origin: "hacktricks"
recipe: "upstream"
build-system: "windows"
meta-checked: "2026-11-13"
github-repo: "MythicAgents/Apollo"
github-stars: 563
github-match: "exact"
gh-checked: "2026-11-13"
worth: 5.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
license: "BSD-3-Clause"
---

# apollo

> Windows post-exploitation agent in C# (.NET Framework 4.0) for the Mythic C2 platform.

## What it's for

Apollo is a Windows agent written in C# targeting .NET Framework 4.0,
designed to plug into a Mythic installation (it originated for
SpecterOps training offerings). Once installed it gives the operator
file operations, credential access (`dcsync`, `mimikatz`, `pth`),
execution of .NET assemblies / PowerShell / unmanaged PEs / COFF
object files, switchable process-injection techniques, token
manipulation, SOCKSv5 routing, and P2P linking over SMB or TCP.
Supported C2 profiles are HTTP, HTTPX (malleable), SMB, TCP and
WebSocket. It is not a standalone implant: a running Mythic server is a
prerequisite, and agent commands are taskable only through the Mythic
UI/API.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Install the agent into Mythic

From the Mythic install directory, add Apollo and restart.

```
sudo -E ./mythic-cli install github https://github.com/MythicAgents/Apollo.git
```

Outcome: Apollo is registered as a payload type in Mythic; after
restarting Mythic you can generate Apollo payloads (choosing a C2
profile) from the `Create Payload` view.

### DCSync credentials from a domain

Dump one or more users' Kerberos secrets through the DC.

```
dcsync -Domain corp.example.com -User user
```

Outcome: the agent performs a DCSync replication against a domain
controller (optionally pinned with `-DC dc.corp.example.com`) and
returns hash material for the targeted account(s) to the Mythic
callback.

### Run cached post-exploitation binaries

Register files once, then execute them repeatedly without re-transfer.

```
register_file
execute_assembly -Assembly Rubeus.exe -Arguments kerberoast
```

Outcome: `register_file` stores the binary in the agent's file cache
(DPAPI-encrypted AES256 blobs, so signatures are not exposed outside
task execution); `execute_assembly` runs the cached .NET assembly
fork-and-run style and returns its output. The same cache feeds
`execute_pe`, `inline_assembly`, `powerpick` and `execute_coff`.

### Route traffic through the callback

Stand up a SOCKS proxy reachable from the Mythic server.

```
socks -Port 1080
```

Outcome: Apollo starts a SOCKS server routed through the Mythic server
on the given port, usable with `proxychains4`; terminate it from the
SOCKS page in the Mythic UI.

### Pass-the-hash into a new process

Spawn a process under alternate credentials.

```
pth -Domain corp.example.com -User user -NTLM <ntlm_hash> -AES256 <aes256_key> -Run cmd.exe
```

Outcome: the agent uses mimikatz' pth module to launch the specified
program with the supplied hash (and optionally AES keys), giving a
process running as the targeted user.

### Switch the injection technique

Change how post-exploitation jobs inject into sacrificial processes.

```
get_injection_techniques
set_injection_technique QueueUserAPC
```

Outcome: `get_injection_techniques` lists the registered techniques
and the current default (CreateRemoteThread, QueueUserAPC/Early Bird);
`set_injection_technique` switches jobs that require injection
(`inject`, `assembly_inject`, `psinject`, ...) to the chosen technique.
