---
name: "redfang"
categories:
  - blackarch-bluetooth
version: "2.5"
homepage: "http://packetstormsecurity.com/files/31864/redfang.2.5.tar.gz.html"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2018-11-15"
build-system: make
license: custom
meta-checked: "2026-11-09"
github-repo: "deltj/redfang"
github-stars: 4
github-pushed: "2021-03-22"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# redfang

> Finds non-discoverable Bluetooth devices by brute-forcing the last six bytes of their BD addresses and resolving names.

## What it's for

The @stake "bluetooth hunter" (Ollie Whitehouse, 2003). A device hidden in non-discoverable mode still answers `read_remote_name()` for its own address, so fang sweeps an address range calling that request for every candidate; any reply exposes the device and its friendly name. Ranges can be given as full `from-to` addresses or as `vendor+nnnnnn` using a built-in manufacturer/OUI table, scanning is multithreaded and can spread across several dongles (`hci0`…`hciN`), and results can be written to a text log. It targets classic Bluetooth only — for BLE you need a different tool — and sweeping a 24-bit tail space is slow, so narrowing by vendor prefix matters.

## Typical use cases

### Sweep a full address range

```
sudo redfang -r 00803789EE76-00803789EEff -o found.txt
```

Outcome: probes every address in the range with `read_remote_name()` using the default 10000 ms connect timeout; each device that answers is reported with its name and appended to `found.txt` (without `-o`, results go to stdout). Add `-d` for verbose debug output while scanning.

### Sweep by vendor prefix

```
sudo redfang -r 3com+89EE76 -o found.txt
```

Outcome: expands `3com` to its OUI from the built-in manufacturer table and sweeps that vendor's `008037 89EE76`-style addresses; run `redfang -l` first to list the known manufacturer codes usable in this form.

### Parallelize across several dongles

```
sudo redfang -r 3com+89EE76 -n 2 -t 20000 -o found.txt
```

Outcome: runs two scan threads assumed to sit on `hci0` and `hci1`, splitting the range between them, with the connect timeout raised from the 10000 default to 20000 for reliability at range — roughly halving wall-clock time per dongle added.

*No corpus evidence — derived from upstream documentation.*
