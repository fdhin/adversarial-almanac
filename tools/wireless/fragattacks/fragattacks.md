---
name: "fragattacks"
categories:
  - wireless
version: "git (2025-04-29)"
homepage: "https://github.com/vanhoefm/fragattacks"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: make
meta-checked: "2026-11-09"
github-repo: "vanhoefm/fragattacks"
github-stars: 1335
github-pushed: "2025-04-29"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
worth: 4.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 3
---

# fragattacks

> Mathy Vanhoef's test suite for the Wi-Fi fragmentation and aggregation vulnerabilities (FragAttacks).

## What it's for

FragAttacks (fragmentation and aggregation attacks) is a research test tool by Mathy Vanhoef that checks Wi-Fi clients and access points for the design flaws and implementation bugs disclosed as CVE-2020-24588, -24587, -24586 and CVE-2020-26139 through -26146. The vulnerabilities affect all protected Wi-Fi — WPA2 and WPA3 alike — because they abuse the CCMP/GCMP and fragmentation layers rather than the password. The tool wraps a modified hostap/wpa_supplicant: it runs a test access point or client, injects crafted fragmented and aggregated frames, and tells you whether the device under test accepted them. Tests run as root from the `research/` directory of the source tree, and the tested device may need tcpdump or Wireshark running to verify some results. It is a detection tool only — it proves a device is vulnerable, it does not exploit the flaws to steal data.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Sanity-check the test setup

Before running real attack tests, confirm the tool can reach the device under test at all.

```
./fragattack.py wlan0 --ap ping
```

Outcome: creates an access point named testnetwork with password abcdefgh; connect the client under test to it and the ping test must succeed — if it fails, fix the setup (drivers, network-manager interference, injection) before running real tests. Run `ping I,E,E` next to confirm the device accepts fragmented frames.

### Test a client for the A-MSDU aggregation flaw

Check whether a client can be tricked into parsing an aggregated frame as a normal packet (CVE-2020-24588).

```
./fragattack.py wlan0 --ap amsdu-inject
```

Outcome: the tool's AP waits for the client to connect and injects the cloaked A-MSDU frame; `TEST COMPLETED SUCCESSFULLY` means the device is vulnerable, `Test timed out!` means it is not.

### Test with two network cards in injection mode

Use a second card as the injector when the primary card's driver cannot be patched.

```
./fragattack.py wlan0 --inject wlan1 --ap ping I,E,R,AE --full-recon
```

Outcome: wlan0 acts as the legitimate AP/client while wlan1 injects the cache-attack fragments (CVE-2020-24586) around a deauthentication and reconnect; the same `--inject` form works with every test command.

### Test an access point for mixed-key flaws

Check whether an AP reassembles fragments encrypted under different keys (CVE-2020-24587).

```
./fragattack.py wlan1 ping I,F,BE,AE
```

Outcome: with the target AP configured in `research/client.conf` (a standard wpa_supplicant config), the tool connects as a client and injects the two differently-keyed fragments, reporting the verdict in the same SUCCESSFULLY/timed-out form.
