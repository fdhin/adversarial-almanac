---
name: "fuzzer-server"
gentoo-category: "app-fuzz"
version: "0.1"
homepage: "http://www.atstake.com/"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: scripts
license: GPL-2
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# fuzzer-server

> @stake-era C server that answers HTTP requests with deliberately malformed HTML or WML pages to fuzz clients and proxies.

## What it's for

fuzzer-server is a small single-file C web server, original code from 2002, once distributed via @stake and later packaged by the Pentoo project (which removed it as "long dead"). It listens for requests and replies with corrupted HTML or WML markup, so you point a client at it — a browser, a WAP gateway, or an HTML/WML-processing proxy — and it feeds that client's parser a stream of malformed pages; the fuzzed component is the client, not the server side. Invoking it with the argument `wml` switches page generation from HTML to WML; calling it with no arguments prints its usage summary. The upstream site and the mirrored distfile are both gone; the surviving authoritative material is the archived Pentoo ebuild and its packaging patch (which added `-h`/`--help` handling and argument checking to the 2002 source). No complete, verifiable CLI survives, so treat it as a historical artifact rather than a working recommendation — current client-side markup fuzzing is better served by modern proxy-based toolkits.

*No corpus evidence — derived from archived packaging sources.*
