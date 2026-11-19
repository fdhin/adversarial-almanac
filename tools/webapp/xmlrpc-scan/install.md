---
tool: "xmlrpc-scan"
build-system: "go"
upstream: "https://github.com/devpwn/xmlrpc-scan"
recipe: "upstream"
license: "custom:unknown"
---

# Installing xmlrpc-scan

> Go tool that scans domains or single URLs for open WordPress xmlrpc.php interfaces and SSRF-capable methods.

Source: howtohunt feed; upstream [`devpwn/xmlrpc-scan`](https://github.com/devpwn/xmlrpc-scan). No LICENSE file ships with the repo (checked 2026-11-13). The installed binary is named `xmlrpcscan` (one word).

## Prebuilt binary

Download the compiled binary for your platform from the
[releases](https://github.com/devpwn/xmlrpc-scan/releases) page.

## From source

```bash
git clone https://github.com/devpwn/xmlrpc-scan.git
cd xmlrpc-scan
go build
./xmlrpcscan -target https://target.example.com -server http://bcollab.example.com
```

## Not packaged

No distro package ships xmlrpc-scan (checked 2026-11-13).

Backlinks: [[webapp]] · [[xmlrpc-scan]]
