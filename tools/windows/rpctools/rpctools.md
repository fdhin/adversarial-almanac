---
name: "rpctools"
categories:
  - blackarch-windows
version: "1.0"
homepage: "https://packetstormsecurity.com/files/31879/rpctools.0.zip.html"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-15"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Seabreg/rpctools"
github-stars: 1
github-pushed: "2019-03-24"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rpctools

> BindView's 2001 trio of Windows RPC reconnaissance tools: rpcdump, ifids and walksam.

## What it's for

rpctools is a BindView (Todd Sabin) package from 2001 demonstrating null
session and MSRPC enumeration against Windows NT/2000 hosts. It contains
three console tools: `rpcdump` lists the endpoint-mapper database and can
query every registered RPC server for all interfaces it supports; `ifids`
queries a single RPC server (by protocol sequence and endpoint) for its
interface UUIDs, including servers not registered in the endpoint
mapper; and `walksam` walks the SAM database over the SAMR interface —
the classic null-session user enumeration — via named pipes or the
direct protseqs used by Windows 2000 domain controllers. Everything
modern impacket (`rpcdump.py`-style tooling) still does grew out of this
lineage, but these native Windows binaries remain handy when operating
from a Windows host against legacy targets. BlackArch ships the
prebuilt `.exe` files under `/usr/share/windows/rpctools/`.

GitHub: third-party mirror of the BindView distribution (Seabreg/rpctools).

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Dump the endpoint mapper with interface discovery

Map every RPC service a host exposes.

```
rpcdump -v 198.51.100.10
```

Outcome: prints the target's endpoint-map entries and, with `-v`,
additionally queries each registered server (via RpcMgmtInqIfIds) for
all interfaces it serves — a fuller picture than epdump alone.

### Enumerate interfaces on one endpoint

Query a specific RPC server directly.

```
ifids -p ncacn_ip_tcp -e 1029 198.51.100.10
```

Outcome: connects to the RPC server listening on TCP port 1029 and lists
the interface UUIDs it supports — works even for servers absent from the
endpoint mapper; `-p ncadg_ip_udp` does the same over UDP.

### Enumerate users via null session over SAMR

Walk the SAM database of a legacy host.

```
walksam -p ncacn_ip_tcp -e 1028 198.51.100.10
```

Outcome: dumps user information from the target's SAM over a null
session using the SAMR interface on the given endpoint — find that port
first with `rpcdump -v` by looking for interface UUID
`12345778-1234-abcd-ef00-0123456789ac`; without a protseq, walksam uses
named pipes with your current credentials.
