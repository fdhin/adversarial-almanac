---
name: "wirouter-keyrec"
categories:
  - blackarch-wireless
version: "1.1.2"
homepage: "http://www.salvatorefresta.net/tools/"
packaged-by: [blackarch]
blackarch-builddate: "2019-01-24"
build-system: autotools
license: GPL3
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# wirouter-keyrec

> Recovers default WPA passphrases of ISP routers (Alice AGPF, Fastweb Pirelli/Telsey, Pirelli TeleTu/Tele2, Eircom Netopia) from their SSID.

## What it's for

WiRouter KeyRec (by Salvatore Fresta) computes the factory-default WPA key of supported ISP routers using only information derivable from the broadcast SSID (which encodes serial-derived data): Telecom Italia Alice AGPF, Fastweb Pirelli, Fastweb Telsey, Pirelli TeleTu/Tele2 (these take a `Teletu/<MAC>` form) and Eircom Netopia. It grew out of reverse-engineering research on those routers' key-generation algorithms. It can also invert the process for Alice AGPF: given an SSID, serial number and MAC it derives the "magic numbers" the algorithm needs, and it can refresh its AGPF/TeleTu magic-number tables from the internet. It only recovers unchanged default keys — a user-set passphrase defeats it.

## Typical use cases

### Recover default keys from one or more SSIDs

Pass SSIDs directly on the command line, comma-separated.

```
wirouterkeyrec -s Alice-58064234,FASTWEB-1-00036FB733FC,eircom26337520
```

Outcome: prints, for each recognised SSID, the computed default WPA passphrase of that router; TeleTu networks are addressed as `Teletu/00:23:8E:E5:28:C8` instead of by plain SSID.

### Recover keys for many networks from a list file

Feed a file with one SSID per line (optionally mixed with `-s` entries).

```
wirouterkeyrec -f networks.lst
```

Outcome: computes and prints the default key for every SSID in the file that matches a supported model; unmatched SSIDs are skipped.

### Derive AGPF magic numbers for a specific unit

When the AGPF magic numbers for a serial range are unknown, derive them from an observed SSID, serial and MAC pair.

```
wirouterkeyrec -m Alice-48230959/67903X0041857/00:23:8e:89:1f:ec
```

Outcome: prints the calculated magic numbers; add `-p` to format them like the AGPF configuration file, or `-w` to append them to it so future `-s` runs cover that unit. `wirouterkeyrec -u` refreshes the shipped AGPF table from the internet instead.

*No corpus evidence — derived from upstream documentation.*
