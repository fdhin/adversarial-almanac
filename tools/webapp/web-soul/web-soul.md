---
name: "web-soul"
categories:
  - blackarch-webapp
version: "2"
homepage: "http://packetstormsecurity.com/files/122064/Web-Soul-Scanner.html"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# web-soul

> Plugin-driven, menu-based Perl console for web application attack and data mining (IrIsT Security Team).

## What it's for

Web-Soul 2 (coded by silent, IrIsT Security Team) is an interactive Perl console that organizes dozens of small plugins into six menus: WebApp Scanners (LFI, RFI, SQL injection, Joomla, WordPress, MyBB, PHP-Nuke, JCE), Site Crawler, Exploiters (fuzzylime, wp-ripe), Server Scanners (Oracle DB, IMAP, VMware Server, and other service probes), Tools (admin-page finder, mailer, ddoser, vBulletin/MyBB utilities), and Help/About/Support. It is a 2013-era tool aimed at the PHP applications of that period, so many plugins target versions long out of support — treat it as a historical curiosity and an example of plugin-organized attack consoles rather than a modern scanner. It has no command-line options: everything happens in the numbered menu after launch.

## Typical use cases

### Open the console

Start the interactive toolkit.

```
web-soul
```

Outcome: prints the banner and the numbered main menu (1 WebApp Scanners, 2 Site Crawler, 3 Exploiters, 4 Server Scanners, 5 Tools, 6 Help/About/Support); choose a number, pick a plugin from that section, and answer its prompts for the target — the packaged launcher runs `web-soul.pl` from `/usr/share/web-soul/` under Perl.

*No corpus evidence — derived from the packaged upstream source (PacketStorm web-soul-2.zip: web-soul.pl and the webapp/, crawler/, exploiters/, server/, tools/ plugin tree).*
