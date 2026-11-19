---
technology: "distcc"
kind: overview
audience: offensive-security
family: network-services
layer: "distributed compilation daemon, distccd (TCP/3632, plaintext, no protocol authentication)"
standards: "distcc protocol v1/v2/v3 (DIST/ARGC/ARGV/DOTI/DONE/STAT/SERR/SOUT/DOTO); GPL-2.0; CVE-2004-2687"
homepage: https://distcc.github.io
github-repo: https://github.com/distcc/distcc
status: thin
provenance: "corpus: ZERO writeups under the command-context gate (2026-11-12; markers distcc, distccd, 3632/tcp, DISTCC_*, --script distcc) — the structure-pass count of 4 was pure digit noise (a 'performance_36322852...dll'-style temp filename; perf counters like '3632699/3632920'; a 'RecordID: 11363271' database line; a Postfix ESMTP id containing '8043632CCF'; plus hex-tail collisions in two other files). Unit retained as thin first-class per standing rule; content is entirely upstream-grounded. Upstream-verified: MITRE CVE-2004-2687 record (distcc 2.x / XCode 1.5, 'compilation jobs ... executed by the server without authorization checks'; 2004-08-26 distcc-list 'got compromised' thread); distcc official security notes (anyone who can connect can run arbitrary commands as the distccd user; 3.0+ refuses to run without --allow; DISTCC_CMDLIST); metasploit distcc_exec source (DIST00000001/ARGC/ARGV wire format, sh -c payload, RPORT 3632); distcc protocol-3 spec and distccd(1); nmap-service-probes DistCCD probe. Enrichment pass 1, wave 4. Supplement (2026-11-13) rewritten from HackTricks (src/network-services-pentesting/3632-pentesting-distcc.md), CC BY-NC 4.0, accessed 2026-11-13 (3.3 masquerade gate, --enable-tcp-insecure/--allow-private/--auth flags, EOF-vs-refusal diagnostics, distcc-cve2004-2687 NSE — verified against distccd(1) and the nmap script source)."
---

# distcc

> A compile farm daemon whose wire protocol is a job request with no authentication of any kind — in the 2.x era, anyone who could reach port 3632 could put `sh -c <command>` where the compiler name goes, and the daemon would run it (CVE-2004-2687).

## What it is

distcc is a distributed build tool for C/C++/Objective-C: a client ships preprocessed source to a **distccd** daemon, the daemon runs the compiler, and ships back stdout, stderr and the object file. It was written by Martin Pool at Samba-project origins (GPL-2.0) and became famous in two security contexts: as the build accelerator behind large Gentoo/XCode-era farms, and as the service that got those farms compromised — the August 2004 distcc mailing-list thread titled "Exploit in distcc (got compromised ;(" is cited in the CVE record, and the same weakness is why distccd 3.0 refuses to start without an `--allow` CIDR today.

The wire protocol is the vulnerability. A job request opens with the magic `DIST00000001`, then `ARGC` (argument count as 8 hex digits), then one `ARGV<len-as-hex><bytes>` token per argument — the client's entire compiler command line — then `DOTI` (the source). The server answers `DONE`, `STAT` (exit status), `SERR` (stderr bytes), `SOUT` (stdout bytes), `DOTO` (object bytes). There is no handshake, no authentication, no authorization anywhere in the protocol: the official security notes state it plainly — *"Anyone who can connect to the distcc server port can run arbitrary commands on that machine as the distccd user."* CVE-2004-2687 formalizes the 2.x-era behavior (as shipped in XCode 1.5 and others): compilation jobs are executed without authorization checks. The metasploit module distcc_exec weaponizes it by sending argv = `sh -c <payload>` padded with `# -c main.c -o main.o` so the trailing arguments look like a compile; the daemon executes argv[0] and returns your command's stdout in `SOUT`. Note the myth-busting detail for scoping: the protocol token is **ARGV** (with ARGC) — there is no "DISTCC_ARGS" field — and `--log-stderr` is a real but mundane daemon logging flag ("mainly intended for use in debugging. Do not use in inetd mode"), not an exploit primitive.

The operator takeaway: distccd is a **pre-auth remote command execution as an unprivileged user** wherever a 2.x-era daemon (or a 3.x daemon with a permissive `--allow`) is reachable. The compensating controls have precise version gates — 3.0+ refuses to run without `--allow`; Debian/RPM packages set `DISTCC_CMDLIST` (via `/etc/distcc/commands.allow.sh`) to restrict executed programs to compiler names; the daemon drops privileges to `nobody` or a dedicated `distcc` user — so the finding severity reads directly off the version banner and the listener config. Also in the official notes: TCP mode is unencrypted and unsigned, so a network position is *passively* source-code disclosure and *actively* job tampering — a MITM can rewrite builds.

## The anatomy that matters on an engagement

    DIST00000001                       ← protocol magic (v1; v2 adds compression, v3 sends raw files)
    ARGC<hex-count>
    ARGV<len-hex>sh                    ← argv[0] should be "cc"; nothing enforces it in 2.x
    ARGV<len-hex>-c
    ARGV<len-hex><command>             ← the payload — executed server-side
    ARGV...  # -c main.c -o main.o     ← comment padding so the tail parses as a compile
    DOTI<len><source>
      ← DONE00000001 STAT00000000 SERR<len><stderr> SOUT<len><stdout> DOTO<len><object>

Three structural properties drive most findings:

- **The authorization model is the firewall.** No credential exists in the protocol; `--allow` CIDRs, `--listen` binding and SSH transport are the only gates, and only 3.0+ enforces that an `--allow` was set at all.
- **The response channel is built-in output exfiltration**: `SOUT`/`SERR` return your command's stdout/stderr over the same socket — no callback needed to verify execution.
- **Trust runs both directions.** The official notes warn the *client* also trusts the *server* with its source (and a malicious server could poison object code) — in a build farm, compromising one distccd compromises every binary the farm produces.

## Where you'll meet it

- **Build farms and CI runners** — organically grown distcc clusters on developer VLANs; the 3632 listener is often world-reachable within the org because `--allow` was never narrowed.
- **Vendor/appliance networks and old infra** — 2.x-era daemons (pre-2007, the XCode 1.5 generation) are unpatched-by-definition: pre-auth RCE as `nobody`.
- **Compromised-build supply chains** — the inverse attack: as the "server" in a client's `DISTCC_HOSTS`, feed back trojaned objects.
- **Detection contexts** — nmap's version scan has a dedicated DistCCD probe for 3632 (see below), so the surface shows up in any competent `-sV` sweep of a dev segment.

## Reading the daemon's mind

1. **The banner answers a fake compile**: nmap's DistCCD probe sends `ARGV cc -c nmap.c -o nmap.o` and matches the `DONE00000001 STAT... SERR... SOUT... DOTO` reply — meaning service detection itself causes a compiler execution on the target; some match rules even lift the `GCC:` version string out of SOUT.
2. **3.0+ without `--allow` exits at startup** — a 3632 listener that stays up is 2.x (vulnerable generation) or a 3.x someone deliberately opened; either way the next question is which CIDRs `--allow` permits.
3. **Privilege is dropped before serving**: root-started distccd serves as `nobody`/`distcc`/`--user` — plan the post-exploitation as an unprivileged shell (kernel privescs, misconfig hunts).
4. **`DISTCC_CMDLIST` is a soft fence**: when set, distccd only serves programs in the list — but the project itself says not to rely on it, since gcc is not hostile-input-safe (a crafted source or flag line can still escape into compiler behavior).
5. **Logging flags are recon targets, not attacks**: `--log-file`, `--log-stderr`, `--log-level` define where your jobs got recorded; after exploitation, check the log destination for your traces. See the [[distcc-bug-bounty|methodology]] for the raw-socket proof and the metasploit run.

## Where to go next

[[distcc-bug-bounty|distcc bug bounty methodology]] — detection via nmap's DistCCD probe, the hand-rolled DIST/ARGC/ARGV proof-of-execution, the metasploit distcc_exec chain, and post-exploitation around the daemon's privilege drop.
