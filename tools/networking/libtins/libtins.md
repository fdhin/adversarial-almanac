---
name: "libtins"
categories:
  - blackarch-networking
version: "1308.3111a90"
homepage: "https://github.com/mfontanini/libtins"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-08-24"
build-system: cmake
license: BSD
meta-checked: "2026-11-09"
github-repo: "mfontanini/libtins"
github-stars: 1992
github-pushed: "2026-08-05"
github-archived: false
github-issues: 174
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 3
gh-activity-checked: "2026-11-09"
---

# libtins

> High-level C++ library for sniffing, crafting and sending network packets without touching raw socket APIs.

## What it's for

libtins gives C++ developers an endian- and platform-independent way to build tools that send, receive and manipulate crafted packets, on top of libpcap and OpenSSL. It parses packets into typed PDU layer objects (Ethernet/IP/TCP/DNS/DHCP/...), supports modification and re-serialization of captured packets, and includes higher-level helpers: sniffers with BPF filters and offline pcap reading, TCP stream reassembly, a TCP ACK tracker, dot11/RadioTap parsing and WPA2 decryption. There is no `libtins` command — it is a library (`-ltins`, headers under `tins/`) consumed by your own programs; the upstream repository ships a rich `examples/` directory (SYN port scanner, ARP spoofing/monitor, DNS sniffer and spoofer, traceroute, WPS detector) and tutorials at libtins.github.io that these examples mirror.

## Typical use cases

### Build a DNS query sniffer against the installed library

```
g++ -o dns_queries dns_queries.cpp -ltins && sudo ./dns_queries eth0
```

with `dns_queries.cpp` being the upstream example core:

```cpp
#include <tins/tins.h>
#include <iostream>

using namespace Tins;

bool callback(const PDU& pdu) {
    DNS dns = pdu.rfind_pdu<RawPDU>().to<DNS>();
    for (const auto& query : dns.queries())
        std::cout << query.dname() << std::endl;
    return true;
}

int main(int argc, char* argv[]) {
    SnifferConfiguration config;
    config.set_promisc_mode(true);
    config.set_filter("udp and dst port 53");
    Sniffer sniffer(argv[1], config);
    sniffer.sniff_loop(callback);
}
```

Outcome: compiles against the shared library, then prints every domain name queried by any host whose port-53 traffic crosses `eth0` — the minimal pattern for any BPF-filtered sniffer with protocol decoding.

### Enumerate interfaces and their addressing from C++

```
g++ -o interfaces_info interfaces_info.cpp -ltins && ./interfaces_info
```

using the upstream example that iterates `NetworkInterface::all()` and prints each interface's `info()` — HW/IP/IPv6 address, netmask, broadcast, index and up/down status.

Outcome: a libtins-powered replacement for scraping `ip addr` output, giving structured per-interface data your tool can pick a sniffing or sending interface from.

### Reuse the shipped example tools as reference implementations

```
ls examples/
```

Outcome: the upstream source tree's `examples/` directory holds ready-made SYN scanner, ARP spoofing/monitor, DNS spoofer, traceroute and WPS-detector programs — each a working template for the corresponding crafting or sniffing pattern, documented alongside the tutorials at libtins.github.io.

*No corpus evidence — derived from upstream documentation.*
