---
name: "asp-audit"
categories:
  - blackarch-fingerprint
version: "2BETA"
homepage: "http://seclists.org/basics/2006/Sep/128"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# asp-audit

> ASP.NET fingerprinter and configuration checker (Perl) — point it at one .aspx URL and it reports the framework version plus several weak server settings.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Quickly profiling a Microsoft ASP.NET web application during reconnaissance. Given a single application URL, asp-audit (by David Kierznowski, 2006) identifies the server software, whether ASP.NET is in use, the framework version (both the coarse build and, when disclosed, the specific build), and a handful of weak-configuration signals: verbose ASP.NET error messages, default error pages, and ASP.NET validation. Any discovered build number is matched against a built-in table of known .NET releases, and `-bf` brute-forces `/aspnet_client/system_web/` directories to reveal every framework build present on the host. It is a single-target, ASP.NET-only prober — not a general web scanner, and it does nothing with non-ASP.NET stacks.

## Typical use cases

### Profile an ASP.NET application

Run the standard check against one application URL.

```
asp-audit http://target.example.com/app/page.aspx
```

Outcome: prints a `[ .NET Configuration Analysis ]` report listing server software, ASP.NET framework presence, simple and specific ASP.NET versions, and whether verbose messages, default error pages and validation are enabled; a disclosed build number is also matched against known .NET releases (`matches -> ...` lines).

### Brute-force installed framework builds

Enumerate which ASP.NET builds exist on the server beyond what headers disclose.

```
asp-audit http://target.example.com/app/page.aspx -bf
```

Outcome: after the configuration report, requests `/aspnet_client/system_web/<build>` for every known ASP.NET build and prints a `Found ->` line for each directory that exists — every hit is a framework version installed on the server.
