---
technology: "IIS (Internet Information Services)"
kind: overview
audience: offensive-security
family: windows
layer: web-server
language: "native (http.sys) + managed (ASP.NET)"
first-released: 1995 (IIS 1.0, Windows NT 3.51)
license: proprietary (ships with Windows Server)
homepage: "https://www.iis.net"
provenance: "field-grounded (63 evidence files with command-context evidence; 67 raw marker files) + upstream: Soroush Dalili's tilde-disclosure paper and web.config research, MSRC bulletins MS10-065/MS10-070, CVE-2017-7269 advisories; verified 2026-11-12"
---

# IIS (Internet Information Services)

> The Windows web server whose per-directory `web.config` means "any directory you can write to is a directory you can reconfigure" — and whose NTFS roots leak 1990s short filenames through the URL parser.

## What it is

IIS is Microsoft's web server, bundled with Windows Server since 1995 (IIS 1.0 on NT 3.51) and developed alongside the OS: the version number is effectively an OS fingerprint. IIS 5.x rode Windows 2000/XP, 6.0 rode Server 2003, 7.0/7.5 rode Vista/Server 2008/2008 R2, 8.0/8.5 rode Server 2012/2012 R2, and 10.0 spans Server 2016 through 2022+ (the `Server: Microsoft-IIS/10.0` banner has been stable for a decade — it dates a box as "2016-or-newer", not a precise build). In the evidence pool's live targets, 84 banner hits read `Microsoft-IIS/10.0`, with 7.5 (Server 2008 R2) and 8.5 (Server 2012 R2) showing up on unpatched holdouts.

The one-paragraph mental model: a kernel-mode listener (`http.sys`) accepts every connection and hands requests to worker processes (`w3wp.exe`) running application pools. Each pool loads a stack of handlers, and **the file extension decides which handler runs** — `.aspx`/`.asp`/`.ashx`/`.asmx` map to script processors, everything else to the static-file handler. Crucially, that mapping is not global: every directory can carry a `web.config` that adds, removes, or overrides handlers, request-filtering rules, and ASP.NET settings for that subtree. The operator takeaway: IIS's delegated-configuration design converts *any writable directory* into *arbitrary configuration* — upload a `web.config` next to your file and you choose its interpreter (the classic: map `*.config` to the classic-ASP ISAPI DLL and put ASP code inside the config itself).

The third leg is NTFS itself. Windows still generates 8.3 short names (`TRANSFER~1.ASP`) and alternate data streams (`file.aspx::$DATA`), and IIS's URL parser historically resolved them — which is why a 2010-vulnerability family (tilde disclosure, found by Soroush Dalili and Ali Abbasnejad) still enumerates hidden files on modern builds via a bare `OPTIONS /*~1*/.aspx`.

## The anatomy that matters on an engagement

    client → http.sys (kernel listener, all ports/sites)
           → w3wp.exe (worker process, app-pool identity — often a virtual account)
           → handler map: extension → static file | asp.dll | ASP.NET (aspnet_isapi)
             (each directory's web.config rewrites this map for its subtree)
           → request filtering (fileExtensions, hiddenSegments, requestLimits)
           → app code / ISAPI / ViewState postback

Three structural properties drive most findings:

- **Extension = execution, config = extension.** Upload filtering that blocks `.aspx` but admits any config-relevant name (`.config`, `.ashx`, `.asa`, `.cer`, `.soap`) loses to a one-line handler remap. The canonical observed chain: only `.config` uploads accepted → upload a `web.config` that routes `*.config` through `asp.dll` → the config file itself executes ASP.
- **Delegated configuration is per-directory and additive.** `web.config` inherits down the tree; a config you plant in `/uploads/` does not touch the application root — which is exactly why it works quietly.
- **NTFS artifacts pass through the parser.** Tilde short names (`*~1*`), `::$DATA` streams, and the `::$Index_Allocation` suffix on directory names leak or bypass authentication on vulnerable builds — pre-auth file *existence* oracle, no exploit code needed.

## Where you'll meet it

- **Internet-facing Windows Server hosting ASP/ASP.NET** — the default target profile: `Microsoft-IIS/x.y` banner plus an upload feature, a legacy `.aspx` app, or a forgotten `aspnet_client` directory. Implication: your chain is fingerprint → shortname/web.config recon → upload or ViewState RCE; ASP.NETViewState needs the machineKey, so config disclosure is the whole game.
- **Internal line-of-business apps behind the perimeter** — SharePoints, report portals, self-written intranet tools on 2008 R2-era boxes. Implication: versions stop at IIS 7.5/8.5, patching is stale, and the kernel-exploit route after a webshell foothold (an observed privesc: `local_exploit_suggester` → two working kernel exploits) is live.
- **Pivot targets inside an AD estate** — the app-pool identity and any hardcoded connection strings in leaked `web.config` files are domain-credential material; an IIS box is usually also an SMB/LDAP-reachable box.

## Reading the framework's mind

Five facts predict an IIS target's wire behavior — each expands into a phase of [[iis-bug-bounty|the methodology]]:

1. **The `Server: Microsoft-IIS/x.y` header is on by default** and pins the OS generation (7.5 = 2008 R2, 8.5 = 2012 R2, 10.0 = 2016+). It is the first line of every recon section.
2. **A tilde (`~`) in the URL path is an existence oracle.** `GET/OPTIONS /*~1*/.aspx`: 404 = exists, 400/200 = not (IIS 6 vs newer differ — the scanner encodes both). Reveals the first six characters plus extension of every file in the root.
3. **`web.config` is the crown-jewel file, and it is everywhere.** Traversal downloads (`?fileName=../../Web.config`), backup copies (`web.config.old`, `web.config.txt`), and static-handler misconfigurations all hand you connection strings and `<machineKey>` — and a leaked machineKey is unsigned-ViewState forging, i.e. RCE.
4. **Extension filtering without config filtering is not upload security.** The IIS execution ladder (`aspx asp ashx asmx asa cer soap config`) plus parser quirks (`file.asp;.jpg`, trailing dot, `::$DATA`) gives an upload bypass for nearly every denylist.
5. **`/aspnet_client/` existing at the root proves ASP.NET is wired** — and is itself a frequent dirsearch hit that separates "IIS hosting static content" from "IIS executing managed code".

## Where to go next

[[iis-bug-bounty|IIS bug bounty methodology]] — fingerprinting and version-to-OS mapping, the tilde shortname scan (manual + scanner), web.config hunting and what to extract from it, the upload-execution ladder with the config-handler-hijack PoC, machineKey → ViewState RCE with YSoSerial.Net, and the CVE history that still matters (ExplodingCan, padding oracle, FastCGI).
