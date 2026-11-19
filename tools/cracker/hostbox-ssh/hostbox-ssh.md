---
name: "hostbox-ssh"
categories:
  - blackarch-cracker
version: "0.1.1"
homepage: "http://stridsmanit.wordpress.com/2012/12/02/brute-forcing-passwords-with-hostbox-ssh-1-1/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# hostbox-ssh

> SSH credential scanner with console mode, GUI mode and nmap log import

## What it's for

hostbox-ssh tests lists of SSH credentials at scale: give it a file of target hosts plus comma-separated usernames and passwords, and it attempts every combination over SSH using paramiko, logging only successful logins. The host file can be a plain list or an nmap greppable (`-oG`) scan log, so the tool slots in directly after a port sweep for open SSH. Two password tokens cover the most common weak-credential cases without editing the list: `blank` sends an empty password and `-username` substitutes the username being tried as its own password. It speaks SSH only, and its Python 2 / wxWidgets stack marks it as a legacy tool.

## Typical use cases

### Scan a host list with credential lists

```
hostbox-ssh -i hosts.txt -u root,admin,guest -p blank,-username,Password1
```

Outcome: reads one host per line from hosts.txt, tries each user against each password (empty password for `blank`, the current username for `-username`), prints `On Host: <host> found login combination: <user> / <pass>` for successes and writes them to a timestamped log under logs/; failed attempts only print the attempt itself.

### Import hosts from an nmap greppable scan

```
nmap -P0 -p 22 -oG sshscan.log 198.51.100.0/24
hostbox-ssh -i sshscan.log -u root,admin -p blank,password -2
```

Outcome: hostbox-ssh parses the `-oG` log, keeps only hosts with port 22 open, and credential-tests those; `-2` abandons a host after its first successful login and `-1` moves to the next username once that account has logged in — both cut wasted attempts once a foothold is found.

### Open the GUI

Run the scanner interactively without arguments.

```
hostbox-ssh
```

Outcome: opens a wxWidgets window preloaded with users `admin;root;guest` and passwords `blank;password;-username`; scans started there run as background threads and results still land in the logs/ directory.

*No corpus evidence — derived from upstream documentation.*
