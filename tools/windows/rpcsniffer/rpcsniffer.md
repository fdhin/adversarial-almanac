---
name: "rpcsniffer"
categories:
  - blackarch-windows
version: "7.9fab095"
homepage: "https://github.com/AdiKo/RPCSniffer"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-12"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "AdiKo/RPCSniffer"
github-stars: 68
github-pushed: "2014-09-13"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rpcsniffer

> Hook a single Windows RPC server process and watch its RPC messages live in Wireshark.

## What it's for

RPCSniffer (by AdiKo) is a Windows research/analysis tool that captures
Microsoft RPC traffic for one chosen server process — not off the wire,
but by instrumenting the process itself with WinAppDbg (Python 2.7). It
attaches to a PID or process name, hooks the RPC runtime, and streams
every RPC message the process serves (type, procedure number, interface
GUIDs, dispatch-table details, flags and raw data) into a named pipe
that Wireshark reads via the bundled Pyreshark dissector. That makes it
useful for understanding what a given RPC interface actually does —
protocol research, attack-surface mapping on services like spoolsv, and
reversing undocumented interfaces — where a normal network capture only
shows opaque ALPC/named-pipe traffic. It is a proof of concept from
2014, unmaintained, and needs its Python 2.7 + WinAppDbg + Pyreshark
stack installed on the target Windows box. BlackArch ships the source
under `/usr/share/windows/rpcsniffer/`.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Point Wireshark at the RPCSniffer pipe

Prepare the capture side before hooking a process.

```
"C:\Program Files\Wireshark\Wireshark.exe" -i \\.\pipe\RPCSniffer
```

Outcome: starts Wireshark listening on the named pipe RPCSniffer writes
to; leave it at the ready — traffic appears once the hook script below
is running and you press Start.

### Hook an RPC server process by PID or name

Attach the instrumentation to the process serving the interface.

```
python main.py -p 1234
```

Outcome: attaches WinAppDbg to PID 1234 and begins streaming that
process's RPC messages to the pipe; use `-n spoolsv` to select the
process by name instead — every RPC call and response now shows up
dissected in the Wireshark window.
