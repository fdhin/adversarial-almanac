---
technology: "Wi-Fi / 802.11 attack surface"
kind: overview
audience: offensive-security
family: network-services
status: "thin (4 writeup files pass the wireless command-context gate — thin per doctrine, first-class depth)"
layer: radio + credential class — 802.11 reconnaissance and monitor mode, WPA-PSK handshake capture/cracking, WPS PIN attacks (online brute, pixie dust), enterprise WPA (rogue EAP APs, MSCHAPv2 capture), and wireless-config credential reuse
homepage: "https://www.aircrack-ng.org/"
provenance: "corpus-grounded (re-gated 2026-11-12, wireless command-context gate, see internal coverage ledger) + upstream-verified: pixiewps FAQ/repo (pixie-dust attack, Dominique Bongard 2014, E-S1/E-S2 weak-PRNG detail), reaver docs (online PIN split), hostapd README-WPS (ap_setup_locked semantics), aircrack-ng suite docs, wpa_supplicant configuration reference, eaphammer documentation, 2026-11-12."
---

# Wi-Fi / 802.11 attack surface

> The PSK that guards the air is the same string guarding the SSH login — break the radio, own the wire.

## What it is

This unit covers 802.11 as an engagement surface: the reconnaissance that maps who is on the air, the attacks that turn radio proximity into a shared secret (WPA handshake cracking, WPS PIN brute force, offline pixie-dust PIN recovery, rogue-AP credential harvesting), and the aftermath where wireless credentials are reused on infrastructure logins. What distinguishes the modern chains in the wild: you rarely start with a laptop and an antenna. You start with a foothold on a box that *has* radios — an OpenWrt-style router, a consultant workstation, a container with a wlan interface — or with somebody else's capture file already on disk. The attack is the same; the entry is pivot-shaped.

The operator's mental model: 802.11 credentials are low-entropy strings managed by humans, so every wireless success is a credential-reuse success waiting to happen. A PSK cracked from a handshake becomes an SSH password; a WPS-recovered PIN yields the PSK; an MSCHAPv2 response harvested by a rogue access point cracks to a domain-flavored password; a router's wireless config file, found in a backup archive, is simply the password written down. The radio is just the first place the secret appears in recoverable form.

The protocol's characteristic weakness is that its convenience layers undo its crypto. WPA2-PSK is only as strong as the passphrase behind a four-way handshake you can force at will (deauthenticate a client, capture the reconnect, crack offline). WPS reduces the PSK to an 8-digit PIN — and on a large installed base, either an online brute that the AP fails to rate-limit or an offline collapse of the PIN from weak nonce generation (the pixie-dust class). Enterprise mode (WPA-EAP) moves the secret into MSCHAPv2 inside the tunnel, which a rogue AP with a trusted certificate can harvest wholesale.

## The anatomy that matters on an engagement

    position: foothold box with wlan interfaces / consultant laptop / handed-over pcap
      → survey: iw/iwconfig (AP vs client vs monitor roles), iwlist/airodump scan
                (PSK vs MGT rows; WPS information elements)
      → pick target by auth mode:
           WPA-PSK + clients  → deauth (aireplay -0) → airodump handshake capture
                                → aircrack-ng wordlist → airdecap-ng full decrypt
           WPS enabled        → reaver online PIN brute  OR  pixie dust (oneshot/pixiewps)
                                → PIN → PSK
           WPA-EAP (MGT)      → steal AP certs → eaphammer rogue AP → deauth coercion
                                → MSCHAPv2 capture → hashcat -m 5500
      → join: wpa_supplicant config (psk= or identity/password=) → dhclient
      → loot the network behind it: router web UIs, AP SSH, hostapd cred files
      → credential reuse: PSK/domain password → SSH/su elsewhere

Three structural properties drive most findings:

- **Forcing a handshake is cheap and quiet.** Deauthentication costs five frames and the target client does the reconnecting for you; once the capture exists, everything after is offline.
- **WPS converts crypto to arithmetic.** Online: the PIN's two halves plus a checksum digit are ~11,000 guesses an unlocked AP will accept one at a time. Offline (pixie dust): weak E-S1/E-S2 nonce generation on whole chipset families collapses the PIN from a single exchange in seconds.
- **The wireless secret is an infrastructure secret.** Config files (`/etc/config/wireless`, hostapd `eap_user` lists), backup archives, and AP admin panels all restate the same credentials the operators type elsewhere.

## Where you'll meet it

- **Pivoted footholds with radios** — an engagement box exposing wlan interfaces: enumerate roles first (`iw dev`, hostapd/wpa_supplicant services, monitor interfaces, and capability bits — a `reaver` binary carrying `cap_net_raw` in `getcap -r /` output is a gift-wrapped tell).
- **Consultant/field-ops workstations** — multiple radios, diagrams on disk, SNMP descriptions leaking default passwords; the survey starts from their shell.
- **Router and IOT backends** — OpenWrt devices where joining the management SSID lands you on the admin subnet with the LuCI panel and a root account that never got a password.
- **Capture-file loot** — pcaps in tool directories (airgeddon-class installs) and backup tarballs holding wireless configs: handshake or config in hand, no radio needed at all.
- **Enterprise corridors** — MGT-mode SSIDs on 5 GHz: certificate theft plus a rogue AP harvests domain credentials (the MSCHAPv2 family; see [[pptp|the PPTP/CHAP unit]] for the cracking side — same exchange).

## Reading the protocol's mind

1. **`airodump-ng` rows label the whole plan** — `PSK` rows are handshake targets, `MGT` rows are certificate/rogue-AP targets; two BSSIDs for one ESSID means the deauth must cover both.
2. **WPS information elements are readable pre-attack** (`iw <if> scan`: WPS version, config methods Label/Display/Keypad, "Configured" state) — an AP that answers WPS at all is testable for pixie dust in one shot.
3. **A captured PSK is a full decrypt** — `airdecap-ng` with the key rewrites the pcap in cleartext, and the first thing to grep is HTTP cookies (`tshark -Y http.cookie`); session cookies with embedded role claims have been the whole foothold.
4. **`wpa_supplicant` configs are diaries** — `key_mgmt=WPA-PSK` vs `WPA-EAP`, `phase2="auth=MSCHAPV2"`: whatever mode the org uses, the config file states the secret's shape.
5. **WPS lock posture decides the online attack** — hostapd exposes `ap_setup_locked` (1 = locked; unlocked setups brute forever), and many embedded firmwares never lock at all; the durable fix upstream is disabling WPS entirely.

## Where to go next

[[wifi-bug-bounty]] — the phased agent methodology: interface/role enumeration, survey and target selection, handshake capture-and-crack, WPS attacks (reaver, pixie dust), rogue-AP enterprise harvesting, network entry and credential reuse.
