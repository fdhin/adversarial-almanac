---
technology: "OpenVPN"
kind: overview
audience: offensive-security
family: network-services
layer: TLS VPN (tun/tap tunnel, config-file-driven client and server, optional web panels that mint configs)
language: c
first-released: 2001
license: "GPL (community) / proprietary OpenVPN Access Server"
homepage: "https://openvpn.net"
github-repo: "OpenVPN/openvpn"
status: thin
provenance: "field-grounded (4 engagements pass the command-context gate over text+ocr; 5 raw marker hits excluded on manual review — operator-side tun0/TAP artifacts, a window title, and a netstat presence line — see internal coverage ledger; chains: editable world-writable config on a web Test-VPN feature → script-security 2 + up reverse shell as root; admin VPN panel with TOTP → per-user config generation → route alteration for internal pivoting, with the config-generator path /opt/easyrsa/clients/<cn>.ovpn; panel API where the username parameter reaches shell_exec cat of the generated file → command injection, full config incl. CA printed inline; employee-file-share .ovpn loot → sudo openvpn → pushed routes into the internal range; plus source-port 53/4444 firewall-bypass scanning) + upstream-verified (Jacob Baines / Tenable 'Reverse Shell from an OpenVPN Configuration File' for up/script-security semantics, /dev/tcp payload, setenv chunking for Windows, GUI-client differences, vendor's keep-behavior position; nmap reference guide for -g/--source-port scope (raw-socket scans only); man7 ncat page for -p/--source-port; round-trip of proto/port fallback documented by a provider connection page in the corpus evidence). Thin unit treated first-class per standing rule. Pass 2 new-unit wave B, 2026-11-12."
---

# OpenVPN

> The config file is the exploit: hand a stock OpenVPN client a crafted `.ovpn` and `up` executes your command with the privileges of whoever launched it — a VPN connection attached to a root shell.

## What it is

OpenVPN (2001, James Yonan; GPL community edition plus the proprietary Access Server) is the TLS VPN that ate the corporate remote-access world: a C daemon that builds Layer-3 (`tun`) or Layer-2 (`tap`) tunnels over a single UDP or TCP port, authenticated by an X.509 PKI and optionally a second credential. Everything about a connection — peer address, protocol, port, routes, certificates, and scripts to run — is declared in one plain-text config file with the `.ovpn` suffix on the client side. Deployments surround the daemon with self-service web panels that generate those per-user config files, and with easy-rsa PKI directories that hold the keys.

For an operator the product's defining trait is that **the trust model inverts at the config boundary**: the client executes config directives with the launching user's privileges (root, in the common `sudo openvpn` pattern), so every place a config file is generated, stored, edited, or shared is a code-execution boundary. The sibling VPN-protocol unit [[pptp|pptp]] covers the password-attack story of the older tunnel; this one owns configs, panels, PKI, and the network-position tricks around them.

## The anatomy that matters on an engagement

    client ← .ovpn (plain text, the whole attack surface):
        remote <host> <port> / proto udp|tcp
        ifconfig, route …            ← what the client can be told to reach
        auth-user-pass [file]        ← a credential, optionally inline
        <ca> <cert> <key> <tls-auth> ← a full client identity, inline
        script-security 2 + up "<cmd>"  ← arbitrary execution on connect
    server/panel: web UI (login, TOTP) → per-user config generation →
        easy-rsa pki (clients dir, issued certs, CA key) on the same host

Three structural properties drive most findings:

- **Script directives execute as the launcher** (upstream-documented, vendor-intended): `up` runs a command after the tunnel device opens; `script-security 2` permits it. Root-launched clients make the `.ovpn` a root shell in a text file.
- **A config file is a complete credential bundle** when keys are inline — loot one, own the network position; loot the CA, mint unlimited clients.
- **Panels and PKI hosts are soft wrappers** around a hard daemon: the generation endpoints, filename handling, and file permissions around `easy-rsa` are where injection and path abuse live.

## Where you'll meet it

- **Self-service VPN panels** (field-seen twice): a web app that logs users in (sometimes TOTP), generates their `.ovpn`, and hands the daemon its identity. The interesting bugs are almost never in OpenVPN itself but in the panel plumbing — generation endpoints reachable by privilege bump, usernames that reach shell commands, config files printed inline with the CA certificate attached.
- **File shares and home directories** (field-seen): employees keep their working `.ovpn` wherever they keep documents; an internal sharing feature that exposes user files exposes network access with it — `sudo openvpn file.ovpn` and the pushed routes do the rest.
- **Config-test and admin features** (field-seen): web UIs that let a user edit and "test" a config run client-side code with server privileges when the file is world-writable to the web user.
- **Egress-restricted and port-filtered environments**: wherever only certain ports or protocols leave, OpenVPN's free `proto`/port parameters are the standard adaptation — and on the target side, firewall rules that trust source ports become the way in.

## Reading the platform's mind

Five facts predict behavior — each expands in [[openvpn-bug-bounty|the methodology]]:

1. **`up` + `script-security 2` is code execution by design** (upstream write-up and manual): the connection succeeds normally, the log carries only a polite "script-security … may allow … user-defined scripts" note — after your command already ran, as the launching user.
2. **Directive length caps near 256 characters**, so Windows payloads arrive chunked across `setenv` lines and reassemble in `up` (documented technique) — recognition matters when you find a strange multi-`setenv` config.
3. **The client trusts routes**: `route`/`ifconfig` directives in a config or pushed from a server decide what the connected machine can reach — editing routes in a looted config is how field operators pivoted to networks the panel never advertised.
4. **`proto` and port are interchangeable** (`proto udp` ↔ `proto tcp`, any remote port): the TCP/443 fallback is provider-documented standard practice for restrictive egress — expect it on both sides of an engagement.
5. **Source-port matches are a firewall pattern, not a VPN feature**: nmap's `-g/--source-port` (raw-socket scans only — it does nothing for connect/version/script scanning) and ncat's `-p/--source-port` expose services hidden behind rules that trust packets *from* port 53 or 4444 (field-seen: a "closed" pair of ports fronting an SSH service on a high port).

## Where to go next

[[openvpn-bug-bounty|OpenVPN bug bounty methodology]] — panel abuse and generation-endpoint injection, malicious config construction, key/CA loot, route-based pivoting, and the source-port bypass — with the config-file directive reference an operator needs.
