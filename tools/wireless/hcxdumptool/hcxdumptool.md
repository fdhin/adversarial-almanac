---
name: "hcxdumptool"
categories:
  - wireless
homepage: "https://github.com/ZerBea/hcxdumptool"
origin: "pentest-book"
recipe: "upstream"
packaged-by: [arch, nixpkgs]
build-system: make
license: MIT
github-repo: "ZerBea/hcxdumptool"
github-stars: 2228
github-match: "exact"
gh-checked: "2026-11-13"
worth: 8.3
worth-basis: "full (activity+popularity+recency+adoption)"
---

# hcxdumptool

> WLAN capture tool that harvests WPA handshakes and PMKIDs out of the air with layer-2 client/AP attacks — the front end of the hcxdumptool → hcxpcapngtool → hashcat pipeline.

## What it's for

hcxdumptool captures packets from WLAN devices and probes weak points in your own WiFi networks by running layer-2 attacks against the WPA protocol: it connects to access points to request PMKIDs, disassociates clients to force full four-way handshakes, and answers client associations to capture challenges (M1M2) and EAP identities. Output is a pcapng file you convert with hcxpcapngtool (from the companion hcxtools repo) into hash mode 22000 hashes for hashcat or John — hcxdumptool itself does no conversion and no cracking. It is designed to run headless on small boards (Raspberry Pi Zero class) as much as on desktops, needs a monitor-mode + frame-injection capable adapter and a Linux kernel 5.15+, and must run as root. Explicitly out of scope: WEP and WPS cracking (aircrack-ng suite, Reaver/Bully), traffic decryption (run tshark/Wireshark in parallel), evil-twin/rogue-AP work, and full-traffic recording. It expects to be the only tool touching the interface — don't combine it with aircrack-ng tools or macchanger (it randomizes its own MAC space). Upstream's standing warnings apply: it can take a whole WLAN down, so run it only against networks you are authorized to attack and always scope it with a BPF filter.

## Typical use cases

### Recon-scan the air for targets

Get ESSID, channel and MACs of APs and clients before committing to anything.

```
sudo hcxdumptool -i wlan0 --rcascan=active --rds=1 -F
```

Outcome: prints a live scan table (channel, timestamps, client/AP MACs, ESSIDs, in/out of range) so you can pick a target and note its channel and MAC for the filter step; `--rds=5` paces the scan slower to confirm the target is in range.

### Compile a targeting BPF filter

Scope the attack to one MAC — upstream calls this mandatory before attacking.

```
hcxdumptool --bpfc="wlan addr1 aabbccddeeff or wlan addr2 aabbccddeeff or wlan addr3 aabbccddeeff or type mgt subtype probereq" > attack.bpf
```

Outcome: compiles the tcpdump-style expression into BPF instructions and writes them to `attack.bpf`; hcxdumptool (run later with `--bpf=attack.bpf`) then only sees — and only attacks — frames involving that MAC, plus undirected probe requests, which can themselves leak PSK material. To protect a device instead, invert it: `--bpfc="not wlan addr3 aabbccddeeff" > protect.bpf`; test the expression first with `tshark -i wlan0 -f "<same expression>"`.

### Capture handshakes and PMKIDs

Run the actual attack against the chosen AP and channel.

```
sudo hcxdumptool -i wlan0 -c 11a --bpf=attack.bpf -w capture.pcapng
```

Outcome: performs the layer-2 attacks on channel 11 (`-c` takes channel plus band suffix) filtered by attack.bpf, writing everything to `capture.pcapng`; the status display's EA123P columns tick as M1/M1M2/M1M2M3M4 handshakes and PMKIDs land, and Ctrl-C ends the run cleanly with a captured/dropped packet count.

### Tune how invasive the attack is

Turn off individual attack vectors while keeping the capture running.

```
sudo hcxdumptool -i wlan0 -c 11a --proberesponsetx=0 --disable_disassociation --bpf=attack.bpf -w capture.pcapng
```

Outcome: still captures whatever the target emits, but no longer answers clients (`--proberesponsetx=0`) and no longer disassociates them from their AP (`--disable_disassociation`); the remaining vector — connecting to the AP for a PMKID — can be disabled too with `--associationmax=0`, trading stealth for capture yield.

### Hand the capture to the cracking pipeline

Convert the pcapng and crack it offline — the documented workflow's next steps.

```
hcxpcapngtool -o capture.hc22000 capture.pcapng
hashcat -m 22000 --potfile-path=hashcat.wpa.pot capture.hc22000 wordlist
```

Outcome: hcxpcapngtool (from hcxtools) extracts handshake/PMKID records into hashcat's WPA mode-22000 file, printing a capture summary (EAPOL pairs, PMKIDs, warnings about short or over-deauthed captures); hashcat then recovers the PSK from a wordlist — the recovered `ESSID:password` line is the finding.

No corpus evidence — derived from upstream documentation.
