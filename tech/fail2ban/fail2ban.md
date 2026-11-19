---
technology: "fail2ban"
kind: overview
audience: offensive-security
family: host-security
status: "thin (3 evidence files pass the command-context gate, one of them context-tier — upstream carries the methodology)"
layer: host intrusion-prevention daemon (log-watching root service that bans source IPs via firewall actions; local privilege-escalation surface as root)
language: python (daemon), ini-style action/filter configs
homepage: "https://github.com/fail2ban/fail2ban"
github-repo: "fail2ban/fail2ban"
provenance: "corpus-grounded (re-gated 2026-11-12, command-context gate, see internal coverage ledger): two direct chains — group-writable action.d directory plus sudo service-restart rights turned into root RCE on the next ban, and a jail.local mail action (action_mwl, mta=mail) combined with a whois-response injection for root RCE — plus one context-tier engagement reality (root cron consuming fail2ban/ipset ban state) + upstream-verified: fail2ban jail.conf(5) man page (action.d/*.conf [Definition] actionstart/actionstop/actioncheck/actionban/actionunban commands 'executed through a system shell'; jail.conf -> jail.d -> jail.local precedence; banaction default iptables-multiport), CVE-2021-32749 / GHSA-m985-3f3v-cwmm (mailutils mail ~! escape in whois output; affected <=0.9.7, 0.10.0-0.10.6, 0.11.0-0.11.2; patched 0.10.7 and 0.11.3), whois.conf(5) behavior (/usr/local/etc/whois.conf server override), 2026-11-12."
---

# fail2ban

> The daemon that bans you for guessing wrong runs every ban as a root shell command it reads from a config directory — take write access to either half of that arrangement and the next failed login executes your payload.

## What it is

fail2ban is the standard Linux answer to brute force: a root daemon (Python, packaged everywhere) that tails log files, matches auth failures against per-service filter regexes, and after `maxretry` failures within `findtime` "bans" the source IP for `bantime` — where *ban* means executing an action script that inserts a firewall rule (iptables, nftables, firewalld, ipset) and often sends a notification mail. It ships enabled for SSH on Debian-family systems, so it is on by default in exactly the environments you are already spraying credentials at.

The operator's mental model is uncomfortable for the defender: **a root execution engine triggered by the attacker's own failed logins**. Every action file under `/etc/fail2ban/action.d/` is an ini file whose `[Definition]` section holds `actionstart`, `actionban`, `actioncheck`, `actionunban` — command lines the root daemon runs *through a system shell*, with tags like `<ip>` substituted in. That is by design (flexible ban backends) and it is the product's whole privilege story: anyone who can write an action file — or the jail config that selects actions — owns root execution the moment any IP trips a jail. The trigger is not rare; the attacker manufactures it with a handful of bad SSH passwords.

Two configurations dominate real-world compromise. First, the **operations-convenience trap**: teams grant a service account or group the right to restart the daemon (it misbehaves, ops wants `sudo systemctl restart fail2ban` at hand) and separately make the config directory group-writable for template management — the two rights are each harmless-looking and together are a root shell, because action files are only re-read at restart, and the next ban executes them. Second, the **mail-action injection** (CVE-2021-32749): mailing actions like `action_mwl` embed the output of `whois <ip>` into a mail sent with mailutils' `mail` command, and that client interprets `~!` escape sequences in message text — control the whois response and the ban mail executes your command as root.

## The anatomy that matters on an engagement

    your failed auth (ssh, web login, mail, ...) -> /var/log/auth.log etc.
      -> filter.d/<service>.conf regex matches
      -> jail (maxretry within findtime) -> BAN (bantime)
      -> action.d/<action>.conf [Definition]
           actionban = iptables -I f2b-... -s <ip> -j DROP      <- run via system shell AS ROOT
           ... or action_mwl: mail -s ... root <<< "... whois <ip> ..."   <- foreign data in root shell
      -> ban store: iptables/nftables rules, ipset lists, firewalld

Three structural properties drive most findings:

- **Every action line is root shell.** Write access to one action file (or the jail that names it) is code execution waiting for a trigger — and the trigger is a few failed logins you control.
- **Config is read at restart.** The chain needs both halves: write rights to action.d (direct, or via a writable *directory* you can replace files in) and restart rights (sudoers line, init-script rule, group membership). Enumerate them as one primitive, not two findings.
- **Foreign data flows into root context.** Mailing actions interpolate `whois` output — data an attacker can control through whois-server redirection — into a shell command; the whois client even reads a per-host override file (`/usr/local/etc/whois.conf`) that any file-write primitive elsewhere on the box can plant.

## Where you'll meet it

- **Default hardening on SSH-facing Linux** — the sshd jail is enabled out of the box on Debian-family images; every credential spray you run is already interacting with it, and a mid-engagement lockout usually means it just fired.
- **Mail and web-auth front ends** — jails over Postfix/Dovecot/WordPress-style logs; the mail-configured hosts are the CVE-2021-32749 population (`mta = mail`, mailutils installed).
- **Post-foothold privilege escalation** — with any user shell, the checklist is cheap: who owns `/etc/fail2ban/action.d`, what does `sudo -l` allow, what does `jail.local` put in every ban.
- **Honeypot and SOC deployments** — ban state gets consumed by root scripts (published ban lists, archive jobs over `ipset list`), so the daemon's outputs are themselves an enumeration surface; tripping a honeypot jail can be the deployment's intended signal rather than a mishap.
- **Bug bounty nuance:** on a scoped host, the writable-action.d chain is a textbook two-part misconfiguration — report the *pair* (write + restart), with the actual ban-triggered execution demonstrated, and note the default `bantime` you had to wait out.

## Reading the framework's mind

1. **Action files are world-readable and self-describing** — `cat /etc/fail2ban/action.d/*.conf` as any user shows the exact root command lines, the ban backend (default `banaction = iptables-multiport`), and any mail actions; `jail.local` then tells you which actions are live.
2. **A writable action.d *directory* beats file permissions** — you cannot edit `iptables-multiport.conf` if the file is root-owned, but with write permission on the directory you can move it aside and `cp` a replacement into place; check directory ownership before conceding.
3. **The sudoers restart line completes the chain** — `/etc/init.d/fail2ban restart`, `systemctl restart fail2ban`, or `fail2ban-client reload` all re-read the configs; grep `sudo -l` for any of them.
4. **CVE-2021-32749 has a checklist, not a version scan alone** — mailing action that includes whois output (`action_mwl`, `mail-whois`), `mta` resolving to mailutils' `mail`, and a vulnerable release (through 0.9.7, 0.10.0–0.10.6, 0.11.0–0.11.2; fixed in 0.10.7 and 0.11.3); each element is separately visible in config.
5. **Ban state is observable** — `/var/log/fail2ban.log` and the firewall/ipset stores (`ipset list | grep f2b`) show jail names, thresholds and your own bans; as an operator, budget for accidental bans and know the eviction paths (`fail2ban-client unban` needs root — prevention is rate discipline).

## Where to go next

[[fail2ban-bug-bounty]] — the phased agent methodology: confirming the daemon and its jails, the writable action.d + restart chain end to end, the mail-action injection chain with whois redirection, and operational anti-ban tradecraft.
