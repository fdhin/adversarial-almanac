---
technology: "LaTeX / server-side TeX rendering"
kind: overview
audience: offensive-security
family: web-platforms
status: "thin (3 evidence files pass the command-context gate — upstream carries the methodology)"
layer: server-side document rendering (equation/PDF generator services, markdown-to-PDF pipelines, batch compile jobs and their plotting siblings)
language: "TeX macro language (LaTeX, plain TeX — Turing-complete); drivers around pdfTeX/latex/pandoc/gnuplot binaries"
license: "LPPL (LaTeX and packages); TeX's own permissive license"
homepage: "https://www.latex-project.org"
github-repo: "https://github.com/TeX-Live/texlive-source"
provenance: "corpus-grounded (re-gated 2026-11-12, command-context gate, see internal coverage ledger): three distinct chains — listings-package file read through an inline-math equation renderer, unrestricted \\write18 command execution on a pdfTeX-based PDF service, and pandoc raw-LaTeX blocks with a clean \\openin/\\read exfiltration loop under --no-shell-escape, plus a gnuplot .plt cron sibling + upstream-verified: TeX Live web2c manual section 5.5 (restricted shell escape the default since TeX Live 2009: shell_escape = p, --shell-escape/--no-shell-escape, shell_escape_commands whitelist; openout_any = p, openin_any = a), CVE-2016-10243 (TeX Live: mpost in shell_escape_commands -> arbitrary command execution; MITRE/NVD + scumjr 'Pwning coworkers thanks to LaTeX' PoC), Checkoway/Shacham/Rescorla USENIX LEET 2010 'Are Text-Only Data Formats Safe?' (TeX file-I/O primitives, web-previewer exfiltration, TeX virus), gnuplot reference documentation (system/'!' shell execution), LaTeX listings package documentation (\\lstinputlisting), 2026-11-12."
---

# LaTeX / server-side TeX rendering

> Every "paste an equation, get an image" or "submit markdown, get a PDF" service is a Turing-complete macro interpreter with file-reading primitives enabled by default — the only real question is whether shell escape survived the deployment.

## What it is

TeX is Donald Knuth's typesetting engine and LaTeX the macro layer most services actually drive; the engine in production is nearly always pdfTeX (as `pdflatex`) turning user-influenced text into a PDF or PNG. Universities, report generators, invoice builders, wiki exporters and "render my formula" widgets all funnel attacker-controlled text into that engine. TeX's macro language is Turing-complete, and its designers gave it first-class file I/O: macros can open, read and write files on the host running the compile. The academic security literature demonstrated a decade and a half ago that this capability alone — read plus write — is enough to escape the "sandbox" entirely, up to and including a TeX document that infects other documents.

The operator's mental model: **remote code execution with seatbelts of uncertain provenance**. The engine's security posture lives in three knobs. Shell escape (`\write18`, `\input|command`) is disabled or restricted by default since TeX Live 2009 — restricted mode runs only a whitelisted set of helper binaries. File *writing* is clamped by `openout_any = p` (paranoid). File *reading*, though, defaults to `openin_any = a` — **any file the compile user can open**, with shell escape fully off. A stock renderer is therefore an arbitrary-file-read primitive out of the box; execution depends on deployment choices the operator rarely audits.

The design's characteristic mistake is **filtering control-sequence names at the web layer** (`\input`, `\include`, `\write18` on a blocklist) while the engine offers hundreds of equivalent primitives: package macros like `\lstinputlisting`, the pipe-input form, the `\immediate` prefix, or the raw `\openin`/`\read` loop below the typesetting layer. A blocklist compiled by someone who read a blog post is defeated by a primitive from a different blog post.

## The anatomy that matters on an engagement

    user text -> web form / markdown / template
      -> app-side filter (blocklist of control sequences)   <- first bypass target
      -> engine invocation: pdflatex / latex / pandoc -> pdflatex
           flags: -no-shell-escape | (nothing) | -shell-escape
           file READ:  \input, \lstinputlisting, \openin+\read loop   <- default-allowed
           file WRITE: \write, \openout                                <- openout_any = p
           shell:      \immediate\write18{cmd}, \input|cmd             <- shell_escape gate
      -> output artifact (PNG/PDF/error text) = your exfiltration oracle

Three structural properties drive most findings:

- **Reading is the default; execution is the gated exception.** Even a correctly hardened engine (`--no-shell-escape`) reads any path the render user can open, so the file-read finding is nearly always available and the real work is clean extraction.
- **Filters block names, not capabilities.** Math-mode-only services still execute control sequences inside `$...$`; a blocklist on `\write18` misses `\immediate\write18`; a blocklist on `\input` misses the listings package, the pipe form, and `\openin`.
- **Restricted shell escape is not a wall.** The whitelist of "safe" helpers has shipped members that execute arbitrary commands themselves — `mpost -tex=bash` gave full command execution through restricted mode (CVE-2016-10243). Probe the whitelist before concluding "restricted = done".

## Where you'll meet it

- **Equation/formula renderers** — a subdomain or widget that returns a PNG of user LaTeX, often restricted to inline math mode and one-liners; directory listing on the web root frequently leaks the `.tex` boilerplate and package list, which is the blocklist's shopping list.
- **Markdown-to-PDF and report pipelines** — pandoc converting user markdown with raw-attribute blocks enabled (`{=latex}`) before `pdflatex` runs with hardening flags; the raw-block path reopens the engine even when the markdown layer looks safe.
- **PDF generator endpoints** — "create document/invoice/CV" services that compile user `.tex` or templates; execution here is often unrestricted because a needed package demanded `-shell-escape` at install time.
- **Scheduled compile/plot jobs** — version-control-backed document builds and their plotting siblings: a root cron that executes gnuplot scripts found in a writable directory turns any write primitive into root code execution (`system "cmd"` inside a `.plt` file).
- **Bug bounty nuance:** the file read is the reliable finding and its value is the file's sensitivity (credential files, app source, vhost configs); an execution claim must name the shell-escape posture you actually defeated.

## Reading the framework's mind

1. **Error behavior is an oracle.** A blocked control sequence, a missing file and a typesetting failure return distinguishable outputs (a filter message, a "could not generate" image, a log line) — use the differences to map both the filter and the filesystem before extracting.
2. **Math-mode delimiters are part of the payload.** Services advertising "inline math mode" reject bare control sequences but happily compile `$\lstinputlisting{/etc/passwd}$` — wrap, don't abandon.
3. **Leaked invocation flags tell you which family to drop.** A process listing, log line or config revealing `-no-shell-escape`/`-shell-escape`/`-dSAFER` (ghostscript downstream) routes you to the read family or the execution family immediately.
4. **Clean extraction beats typeset extraction.** `\input` mangles content through catcodes, fonts and line-breaking; the `\openin`/`\read`/`\message{\meaning...}` loop returns bytes verbatim and doubles as a blocklist bypass — it is the documented exfiltration technique against web previewers.
5. **The packages a service loads are written in its boilerplate.** `\usepackage{listings}` in a leaked or directory-listed `.tex` file means the verbatim-input macro family is live; gnuplot-adjacent rendering (stats pages, plot services) means the `system` command is one writable directory away from cron-scheduled execution.

## Where to go next

[[latex-bug-bounty]] — the phased agent methodology: fingerprinting the filter, the file-read families (typeset vs verbatim vs below-typesetting primitives), shell-escape triage including restricted-mode escapes, filter-bypass patterns, and the gnuplot `.plt` cron sibling.
