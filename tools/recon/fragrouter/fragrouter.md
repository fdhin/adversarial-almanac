---
name: "fragrouter"
categories:
  - information-gathering
version: "1.7-3kali6"
homepage: "http://www.anzen.com/research/nidsbench/fragrouter.html"
origin: "kali"
recipe: "kali-debian"
packaged-by: [kali]
build-system: make
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# fragrouter

> The 1999 nidsbench IDS-evasion toolkit by Dug Song (Anzen Computing): a one-way fragmenting router that forwards your traffic as fragmented or micro-segmented streams many network IDSs fail to reassemble.

## What it's for

Fragrouter implements the insertion and evasion attacks from the Secure Networks paper "Insertion, Evasion, and Denial of Service: Eluding Network Intrusion Detection" (January 1998). Conceptually it is a one-way router: attack traffic is sent to the fragrouter host, which rewrites it into an equivalent but IDS-hostile data stream — ordered or out-of-order IP fragments, overlapping fragments, 1-byte TCP segments, interleaved SYNs, fake FIN/RSTs, and source-routing variants — before forwarding it to the victim. If the network IDS does not reconstruct IP fragments and TCP streams exactly the way the victim host does, it misses (or mis-parses) the attack. It is used to test IDS reassembly quality, not to find hosts. It is 1999-era C (libpcap/libnet), packaged by Kali for historical and IDS-testing work; run it with no arguments to list every attack it knows.

## Typical use cases

### Baseline forwarding as a control

Run the no-op path first to verify routing works.

```
fragrouter -i eth0 -B1
```

Outcome: `-B1` (baseline-1) forwards packets unchanged on `eth0` — traffic routed through this host toward the victim still passes, so any later alert difference is attributable to the fragmentation attacks, not to setup.

### Forward as ordered tiny IP fragments

Hide the stream from fragment-blind IDSs.

```
fragrouter -i eth0 -F1
```

Outcome: rewrites forwarded traffic into ordered 8-byte IP fragments (`-F2` uses 24-byte fragments, `-F3` through `-F7` add out-of-order, duplicated and overlapping variants) — compare IDS alerts against the `-B1` baseline to see which reassembly gaps remain open.

### Overlapping TCP segments

Evade stream reassembly rather than fragment reassembly.

```
fragrouter -i eth0 -T5
```

Outcome: completes the TCP handshake on behalf of forwarded connections and delivers data as 2-byte segments preceded by 1-byte null-data segments that the forward-overlapping segment overwrites — the classic TCP-stream evasion family (`-T1`..`-T9`, `-C`, `-R` variants interleave SYNs or restart with new sequence numbers).

### Keep full protocol headers in the first fragment

Bypass filters that drop short first fragments.

```
fragrouter -i eth0 -p -F1
```

Outcome: `-p` preserves the entire protocol header in the first fragment while still fragmenting the payload — useful against packet filters that deny short IP fragments outright.

### List every available attack

Discover the full attack catalogue on your build.

```
fragrouter
```

Outcome: prints the version banner and every attack option in its families (`-B` baseline, `-F` frag, `-T` tcp, `-C` tcbc, `-R` tcbt, `-I` insert, `-E` evade, `-M` misc) with a one-line description of each — pick attacks by the reassembly weakness they target.

*No corpus evidence — derived from upstream documentation (packaged 1.6 source tarball; the frontmatter's former GitHub link pointed at an unrelated same-named JavaScript library and was removed — upstream is Anzen Computing's nidsbench, per the Kali package copyright file).*
