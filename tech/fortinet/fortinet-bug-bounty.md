---
technology: "Fortinet FortiOS / FortiGate SSL VPN (sslvpnd attack surface)"
kind: bug-bounty-methodology
audience: ai-agent
status: upstream-only
family: network-security
version: "1.0"
provenance: "upstream-only; zero corpus evidence by design — version gates, mechanisms, workarounds and IoCs transcribed from Fortinet PSIRT FG-IR-18-384 / FG-IR-23-097 / FG-IR-24-015 / FG-IR-22-398 and the Fortinet PSIRT blogs on CVE-2023-27997 and FG-IR-22-398 (fetched 2026-11-13); exploit chain detail from the Bishop Fox and Lexfo writeups those advisories connect to; the 'CVE-2019-1570x backdoor cohort' claim was verified against cve.org CNA records and REFUTED (Phase 5); source: \"six2dez/pentest-book (enumeration/ports.md, Port 541 section, no license — rewritten with attribution), accessed 2026-11-13\""
---

# Fortinet FortiOS SSL-VPN bug-bounty methodology

Companion overview: [[fortinet]].

Scope: the pre-auth HTTPS surface of FortiOS/FortiProxy SSL-VPN (`sslvpnd`), the four critical CVEs with vendor version gates, portal fingerprinting, loot, and compromise forensics. The generic TLS wrapper checks live in [[ssl-tls-bug-bounty]]; the sibling firewall-vendor surface in [[check-point-bug-bounty]].

## Phase 0 — Fingerprint the portal

```bash
nmap -Pn -sV -p 443,8443,10443 198.51.100.10        # HTTPS admin + SSL-VPN listeners
curl -sk https://198.51.100.10/remote/info          # pre-auth platform info
curl -sk https://198.51.100.10/remote/login         # portal page: product fingerprint
```

Decision points:

- `/remote/info` answering pre-auth confirms FortiOS SSL-VPN and leaks platform detail. Research on CVE-2023-27997 shows it also yields a salt derived from `sslvpnd` start time — the last ingredient of that bug's decryption (Phase 2).
- `/remote/logincheck` (credential POST) and `/remote/hostcheck_validate` are the two endpoints that parse the hex `enc` parameter — the 2023/2024 memory-corruption surfaces.
- The administrative GUI and REST API commonly share the same HTTPS listener. The admin-side sibling is FG-IR-22-377 / CVE-2022-40684 (authentication bypass); Fortinet's own reporting on Volt Typhoon lists one IoC pair for it — rogue admin accounts named `fortinet-tech-support` / `fortigate-tech-support`. Check for those accounts in any authenticated enumeration.
- 541/tcp open is a Fortinet SSL-VPN service marker from the vendor's port matrix — a fallback fingerprint when the HTTPS listener is filtered.
- Do not trust the portal's look for versioning; gate every finding on advisory version tables, not page cosmetics.

## Phase 1 — CVE-2018-13379: pre-auth path traversal (FG-IR-18-384)

Vendor facts (verified against the PSIRT page): a path traversal in the SSL VPN web portal's **language resource request** lets an unauthenticated attacker download FortiOS system files via crafted HTTP resource requests. The advisory explicitly acknowledges that exploit code circulating for it "obtain[s] the credentials of logged in SSL VPN users" — the disclosure writeup it links (DEVCORE's Orange Tsai and Meh Chang, who reported the bug) shows the traversal reading SSL-VPN session files that hold usernames and passwords in plaintext.

Affected (gated on SSL-VPN being enabled in web or tunnel mode):

- FortiOS 6.0.0–6.0.4, 5.6.3–5.6.7, 5.4.6–5.4.12 — fix in 5.4.13 / 5.6.8 / 6.0.5 / 6.2.0+
- FortiProxy 2.0.0, 1.2.0–1.2.8, 1.1.0–1.1.6, 1.0.0–1.0.7 (per the CVE record)

Operator sequence: probe the language-resource request with a traversal payload aimed at a session/system file, harvest credentials from any session file returned, then replay them against the portal login. CISA added the CVE to its exploited-vulnerabilities catalog in November 2021 after years of ransomware-crew use — treat a hit as a breach, not a finding. Two-factor authentication on the portal is the vendor's stated mitigation that blunts credential reuse; when 2FA is on, pivot to Phase 2/3/4. Non-destructive validation: request one known file and stop; this bug needs no payload fuzzing to confirm.

## Phase 2 — CVE-2023-27997: pre-auth heap overflow in sslvpnd (FG-IR-23-097)

Vendor facts (verified against the PSIRT page and Fortinet's analysis blog): heap-based buffer overflow [CWE-122] in FortiOS and FortiProxy SSL-VPN **pre-authentication**; CVSSv3 9.8; unauthenticated RCE via crafted requests; discovered externally by Charles Fol and Dany Bach (LEXFO) *and* by Fortinet's own post-incident code audit of the SSL-VPN module launched after the 2022 bug; Fortinet stated it "may have been exploited in a limited number of cases"; CISA listed it as exploited in June 2023. Fix floors: FortiOS 7.2.5 / 7.0.12 / 6.4.13 / 6.2.14 / 6.0.17 (7.4 not affected); FortiProxy 7.2.4 / 7.0.10 / 2.0.13.

Mechanism (from the LEXFO/Bishop Fox exploit research): the `enc` GET parameter on `/remote/hostcheck_validate` and `/remote/logincheck` is hex-decoded into a `fsv_malloc` buffer of `strlen(enc)/2 + 1`, then decrypted with a custom MD5-chained keystream seeded by a salt plus the static string `GCC is the GNU Compiler Collection.` — the salt is obtainable from `GET /remote/info`. The decrypted length field is bounds-checked against the *hex-string* length instead of the decoded buffer length, giving an out-of-bounds write. FortiOS jemalloc (contiguous chunks, LIFO freelists, no hardening) makes the subsequent heap grooming deterministic. Bishop Fox demonstrated a full RCE exploit and reported roughly 490,000 exposed SSL-VPN interfaces at advisory time with most still unpatched a month later.

## Phase 3 — CVE-2024-21762: out-of-bounds write in sslvpnd (FG-IR-24-015)

Vendor facts (verified against the PSIRT page): out-of-bounds write [CWE-787] in FortiOS and FortiProxy; remote **unauthenticated** code/command execution via specially crafted HTTP requests; CVSSv3 9.8; flagged "potentially being exploited in the wild" on publication day (February 2024) and promptly reflected in CISA's exploited-vulnerabilities catalog. Workaround: disable SSL-VPN entirely — the advisory states in bold that disabling web mode is NOT a valid workaround, confirming the bug sits in the daemon's request handling, not the browser-portal layer. The vendor's IPS virtual patch name (`HTTP.Chunk.Length.Invalid.`) points at the bug class: crafted chunked transfer-encoding lengths. Fix floors: FortiOS 7.4.3 / 7.2.7 / 7.0.14 / 6.4.15 / 6.2.16 / 6.0.18; FortiProxy 7.4.3 / 7.2.9 / 7.0.15 / 2.0.14 (1.x all versions: migrate). Public weaponized exploits were scarce at disclosure; validate targets by version gates, not by "no public PoC".

## Phase 4 — CVE-2022-42475: heap overflow exploited in the wild (FG-IR-22-398)

Vendor facts (verified against the PSIRT page and Fortinet's forensic blog): heap-based buffer overflow [CWE-122] in FortiOS SSL-VPN; remote unauthenticated code execution; CVSSv3 9.3; **exploited in the wild** — Fortinet received a compromised appliance from a government-sector victim, and its blog describes an "advanced actor" with highly targeted governmental attacks. Fix floors: FortiOS 7.2.3 / 7.0.9 / 6.4.11 / 6.2.12 / 6.0.16 (5.x and older: migrate); FortiProxy 7.2.2 / 7.0.8 / 2.0.12.

Compromise indicators (from the PSIRT page — run these on any suspect FortiGate):

```text
log:    Logdesc="Application crashed", msg containing
        "application:sslvpnd ... Signal 11 received, Backtrace:"
files:  /data/lib/libips.bak     /data/lib/libgif.so
        /data/lib/libiptcp.so    /data/lib/libipudp.so
        /data/lib/libjepg.so     /var/.sslvpnconfigbk
        /data/etc/wxd.conf       /flash
```

The implant is a generic Linux backdoor customized for FortiOS; `/data/lib/libips.bak` masquerades as the IPS engine library while the real `libips.so` is zeroed. Egress to the advisory's listed external IPs is the network-side signal. Bishop Fox's public exploit for this bug validates targets across versions but only fully detonates on specific old builds — absence of a crash proves nothing.

## Phase 5 — Trap: the "CVE-2019-1570x FortiOS backdoor cohort" does not verify

A recurring third-party claim labels CVE-2019-15704, CVE-2019-15706 and CVE-2019-15707 a "FortiOS backdoor cohort" (unauthorized file download / SSH magic-hash bypass / daemon heap overflow in 5.6.x builds). Verified against the authoritative CVE records (CNA: Fortinet, Inc.), all three IDs belong to different, minor bugs:

- **CVE-2019-15704** — cleartext storage in **FortiClient for Mac** (SSL-VPN session info readable from the console log); FG-IR-19-227.
- **CVE-2019-15706** — stored XSS in the **SSL-VPN portal** of FortiOS/FortiProxy, authenticated attacker, CVSS 4.0; FG-IR-19-223.
- **CVE-2019-15707** — improper access control in the **FortiMail** admin webUI (unauthorized config-backup download); FG-IR-19-237.

None is a FortiOS backdoor. Refuse the mapping in reports, and treat any writeup pairing those IDs with backdoor mechanics as miscited. Late-2019 public research did allege backdoor-like behavior in specific FortiOS 5.6.x builds; this methodology records that the CVE attachment to those allegations fails verification, and that a sibling advisory number (FG-IR-23-130) is *also* commonly mis-cited for CVE-2023-27997 — that number is a FortiSIEM command-injection advisory; the correct one is FG-IR-23-097. Standing rule: resolve every Fortinet CVE↔IR pair against the PSIRT/CNA record before writing it down.

## Phase 6 — Loot: web mode vs tunnel mode, and post-auth

- **Web mode** (clientless): bookmarks and plugin launchers (RDP/SSH/SMB proxies) map the internal application estate; portal session state is the prize. The 2019 traversal's session-file loot (Phase 1) is the canonical example — plaintext credentials for currently-logged-in users.
- **Tunnel mode** (FortiClient): authenticated access yields routes, DNS settings and full internal reachability — after any Phase 1 credential replay, chart the tunnel's allowed subnets; that map is the real deliverable, not the shell.
- **Admin access**: with administrator credentials, the config export names every interface, VPN peer and stored secret; the rogue-account names from Phase 0 double as a quick compromise check.

## Phase 7 — Reporting the fix

Upgrade to at least the per-CVE floors above (vendor upgrade-path tool exists; LivePatch-style partial patches are not a substitute for these), disable the SSL-VPN service entirely where it is unused (both modes — the 2019 and 2024 workarounds only work with the daemon off), restrict the admin GUI/REST API to management addresses, enforce 2FA on SSL-VPN accounts (the vendor's stated counter to Phase 1 credential replay), and hunt the Phase 4 artifacts on anything that was reachable during the exposure window.

## Appendix — tools in prose

`nmap` for listener sweeps and service fingerprints, `curl` for the pre-auth `/remote/` endpoint probes, `openssl` for the TLS wrapper checks (see [[ssl-tls-bug-bounty]]), vendor CLI output from `config vpn ssl settings` for confirming the service state, and the PSIRT pages themselves as the version-gate oracle — keep them open; Fortinet's affected-version tables are the ground truth this methodology cites.
