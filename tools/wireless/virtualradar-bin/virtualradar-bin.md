---
name: "virtualradar-bin"
gentoo-category: "net-wireless"
version: "2.0.1"
homepage: "http://www.virtualradarserver.co.uk/Default.aspx"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: prebuilt
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# virtualradar-bin

> Virtual Radar Server — decodes ADS-B / Mode-S feeds and plots live aircraft on a self-hosted web map.

## What it's for

Virtual Radar Server is an open-source .NET application that runs a
local web server: it takes a feed from a radio or SDR capable of
receiving Mode-S transmissions (Kinetic SBS-1/SBS-3 text and binary
formats, the AVR `*`/`@`/`:` formats, or Mode-S Beast binary) and
plots the aircraft in any modern browser on an interactive map,
enriched with aircraft details from its database. It does not receive
RF itself — pair it with a receiver or decoder that produces one of
those feed formats. On Linux it runs under Mono, either with an X11
GUI or fully headless once the web admin plugin is installed; the
`-bin` package ships the prebuilt Mono tarball.

## Typical use cases

### Start with the desktop GUI

Run it where X11 is available.

```
mono VirtualRadar.exe
```

Outcome: the main window opens; configure the receiver and its feed
under Tools | Options, then open http://127.0.0.1:8080/VirtualRadar/
in a browser to watch aircraft once the feed delivers data.

### Run headless and bootstrap the admin account

Server operation without a GUI (web admin plugin installed).

```
mono VirtualRadar.exe -nogui -createAdmin:user -password:Password1
```

Outcome: VRS starts without a window, creates the administrator
account `user` with the given password, and serves the site on the
default port 8080 — log in at
http://127.0.0.1:8080/VirtualRadar/WebAdmin/Index.html to configure
receivers entirely through the browser.

*No corpus evidence — derived from upstream documentation (virtualradarserver.co.uk: home page feed-format list, Linux/Mono page with `mono VirtualRadar.exe`, `-nogui`, `-createAdmin`/`-password` switches and the web admin URL).*
