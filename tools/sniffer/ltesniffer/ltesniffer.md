---
name: "ltesniffer"
categories:
  - sniffer
version: "git (2024-10-23)"
homepage: "https://github.com/syssec-kaist/ltesniffer"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: make
meta-checked: "2026-11-09"
github-repo: "syssec-kaist/ltesniffer"
github-stars: 2250
github-pushed: "2024-10-23"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 26
---

# ltesniffer

> Open-source LTE downlink/uplink eavesdropper: decodes live over-the-air LTE traffic between a base station and phones into Wireshark-openable pcaps.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Passively decoding real LTE air interface traffic with a software-defined radio. Built on FALCON and the srsRAN library, it first decodes the PDCCH control channel to recover the DCIs and RNTIs of active users, then uses them to decode the PDSCH/PUSCH data channels in both directions — something generic SDR capture tools cannot do — and writes standard pcap files you open in Wireshark. It cannot decrypt encrypted traffic: plaintext parts (broadcast messages, early connection messages, MAC/physical-layer headers) are analyzable, encrypted payloads are not. A security API adds identity mapping (RNTI↔TMSI), IMSI collecting and UE capability profiling for research. Hardware-bound: downlink needs a 2-RX-antenna SDR (USRP B210/X310, BladeRF); uplink additionally needs either one USRP X310 or two GPSDO-synchronized B-series. Check local regulations before sniffing any commercial network.

## Typical use cases

### Sniff downlink traffic from a base station

```
sudo ./src/LTESniffer -A 2 -W 4 -f 1840e6 -C -m 0
```

Outcome: with 2 antennas and 4 worker threads, cell search runs on the given downlink frequency and decoded downlink traffic is written to `sniffer_dl_mode.pcap` in the working directory; open it in Wireshark for packet-level analysis. On a USRP B210 add `-a "num_recv_frames=512"` for stable synchronization.

### Sniff uplink traffic from phones

```
sudo ./src/LTESniffer -A 2 -W 4 -f 1840e6 -u 1745e6 -C -m 1
```

Outcome: both the downlink and uplink frequencies are listened to concurrently (uplink requires the X310 or dual-B-series setup); decoded traffic lands in `sniffer_ul_mode.pcap`, which contains both directions — filter with `mac-lte.direction == 0` (uplink) or `== 1` (downlink) in Wireshark. Phones must be physically near the sniffer for their weak uplink signal to be received.

### Collect identities via the security API

```
sudo ./src/LTESniffer -A 2 -W 4 -f 1840e6 -u 1745e6 -C -m 1 -z 3
```

Outcome: enables all three API functions (identity mapping, IMSI collecting, UE capability profiling); detected identities are printed in a table (identity name, value, message it came from) and captured identity packets are written to `api_collector.pcap`. Lower `-z` values select subsets: 0 identity mapping, 1 IMSI collecting, 2 capability profiling.

### Lock onto one known cell without cell search

```
sudo ./src/LTESniffer -A 2 -W 4 -f 1840e6 -I 379 -p 100 -m 0
```

Outcome: skips the cell-search step and synchronizes directly to the base station with physical cell ID 379 and 100 resource blocks; faster startup when the target cell parameters are already known.
