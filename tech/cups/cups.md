---
technology: CUPS
kind: overview
audience: offensive-security
family: print-infra
layer: print server (IPP over HTTP on TCP 631, discovery on UDP 631, local spooling)
language: C
first-released: 1999
license: "Apache-2.0 (cups); cups-filters components LGPL-2.0+ / MIT"
homepage: "https://www.cups.org"
github-repo: "OpenPrinting/cups"
evidence: "2 writeups (command-context gate: cups-browsed/cupsctl/cupsd config strings, 631/tcp-open and ipp nmap lines, lp/lpstat/lpadmin CLI, CUPS CVE ids; the parent hint of 25 was libcups/lpadmin-group noise — 7 of 10 raw matches were Ubuntu default-group listings, a Go variable named ipp, a closed port, and an env-var string)"
status: thin
provenance: "corpus-mined from 2 writeups in the writeup corpus + upstream-verified: EvilSocket CUPS research (2024-09-26), jfrog.com and oligo.security chain analyses, Gentoo GLSA 201404-01 (CVE-2012-5519), the exploit-chain author's writeup blog, accessed 2026-11-12"
---

# CUPS

> A local root daemon with a web admin UI on 631, a UDP listener that will install any printer the network tells it about, and a document pipeline that historically executes PPD directives as shell commands — three surfaces, and all three have produced full compromise.

## What it is

CUPS (Common UNIX Printing System, originally Michael Sweet / Easy Software Products, 1999; now OpenPrinting) is the standard print stack on Linux, the BSDs, and macOS. `cupsd` (running as root by default on older installs) serves the IPP protocol over HTTP on TCP 631 — usually loopback, sometimes all interfaces — with a web administration UI at `http://127.0.0.1:631`. The `cups-browsed` daemon does legacy printer auto-discovery on UDP 631, and the filters in `cups-filters` (notably `foomatic-rip`, via `libppd` and `libcupsfilters`) convert documents into printer formats.

The operator's mental model: one privileged scheduler, a queue you can ask it to process, and a config (`cupsd.conf`, `cups-files.conf`) editable from the web UI or with `cupsctl`. Membership in the `lpadmin` group is *the* admin boundary on a default install — and the document pipeline has, for over a decade, treated PPD file directives like `FoomaticRIPCommandLine` as things to pass toward a shell.

The takeaway: CUPS is nearly always a **post-foothold or LAN-adjacent target** — the admin UI is loopback-bound but trivially reachable with any port forward, and the 2024 chain turned the UDP discovery listener into unauthenticated remote code execution as the `lp` user on any box where `cups-browsed` was enabled (which, on stock distro installs, it was).

## The anatomy that matters on an engagement

    attacker
      ├─ TCP 631 (IPP over HTTP; web admin at /, /admin, /admin/log/…)
      │     → cupsd (root on old installs) → cupsctl / web directives → ErrorLog → arbitrary file read
      ├─ UDP 631 (cups-browsed auto-discovery, listens INADDR_ANY by default)
      │     → attacker IPP server (ippserver) → Get-Printer-Attributes
      │     → libcupsfilters → libppd → PPD written to disk with injected directives
      │     → queue a job (print test page) → foomatic-rip executes FoomaticRIPCommandLine as lp
      └─ local shell (lp/lpadmin groups) → queue enum, config edit, log read

Three structural properties drive most findings:

1. **Admin authority is file-system authority.** lpadmin-group members (and the web UI) can set directives like the log file path; on CUPS < 1.6.2 nothing stopped `ErrorLog=/etc/shadow`, and since cupsd ran as root, "View Error Log" was arbitrary file read (CVE-2012-5519).
2. **The 2024 chain is a compound design flaw, not one bug.** cups-browsed trusts any UDP packet on 631 (CVE-2024-47176); libcupsfilters (CVE-2024-47076) and libppd (CVE-2024-47175) pass attacker IPP attributes into a generated PPD unsanitized; foomatic-rip then executes `FoomaticRIPCommandLine` (CVE-2024-47177, CVSS 9.9) — but only when a document is actually printed, so the trigger is yours to arrange (web UI "Print Test Page" works, field-observed).
3. **Execution lands as `lp`, not root** — the chain ends in an `lp`-user shell (still an unauthenticated RCE from the network); root still needs a second step.

## Where you'll meet it

- **Linux hosts after a foothold** — `id` shows `lp`/`lpadmin` groups, or `ss`/`netstat` shows 631 loopback. Forward it (chisel/ssh -L) and you have the admin UI. Thinnest coverage: nobody hardens the loopback print server.
- **Exposed 631/TCP on appliances/print servers** — nmap shows `631/tcp open ipp CUPS x.y`; anonymous access to the web UI already leaks printer/queue/version state (one observed deployment showed CUPS 2.4.2 with anonymous web access).
- **UDP 631 on any stock distro with cups-browsed** — the RCE chain entry; single unicast packet, no auth.
- **macOS/BSD print services** — same code paths, often forgotten in scope definitions.

## Reading the framework's mind

- The web UI at `http://127.0.0.1:631` distinguishes `Home` (state, printers) from `Administration` (needs lpadmin credentials — but on old CUPS, local group membership was often enough).
- `cupsctl` is the CLI knob for the same directives as the admin UI — including `ErrorLog` (CVE-2012-5519 abuse primitive) and, in the 2024 RCE chain, the printer the injected PPD belongs to.
- Queue state is observable from the UI (`Printers → <printer> → Print Test Page`): the 2024 chain's execution step fires when a job for the malicious printer runs — queuing the test page yourself is the standard trigger.
- Version tells you which surface: **< 1.6.2** → ErrorLog file read path; **cups-browsed ≤ 2.0.1 / libcupsfilters ≤ 2.1b1 / libppd ≤ 2.1b1 / cups-filters ≤ 2.0.1** → the 2024 UDP chain (patched in the Sept–Oct 2024 releases; many distros shipped cups-browsed *disabled* in response).
- `lpstat -t` / `lpstat -p -d` enumerate printers and queues as any local user; `lpadmin` manages them — these are the low-impact recon commands when you land as an lp-family user.

## Where to go next

[[cups-bug-bounty|CUPS bug-bounty methodology]] — phased: fingerprint 631/TCP+UDP → admin-UI and ErrorLog abuse → the CVE-2024-4717x UDP chain with a working ippserver shape → queue enum → disclosure notes.
