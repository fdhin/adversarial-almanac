---
name: "dns2geoip"
categories:
  - blackarch-scanner
version: "0.1"
homepage: "http://packetstormsecurity.com/files/118036/DNS-GeoIP.html"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "Seabreg/dns2geoip"
github-stars: 0
github-pushed: "2019-03-17"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dns2geoip

> Brute-force a domain's subdomains, then plot the ones that resolve on a map via their GeoIP locations.

## What it's for

dns2geoip is a single-script pipeline that chains two jobs: it resolves
`<word>.<domain>` for every entry in a subdomain wordlist (dnspython), then
looks up each resolving IP in a MaxMind GeoIP City database and writes the
coordinates as a KML file you can open in Google Earth. Use it to scope an
organisation's external footprint — which services are hosted on site and
which are offsite — rather than as a general subdomain enumerator. It needs
a local GeoIP city database (default path
`/usr/share/GeoIP/GeoIPCity.dat`) and is Python 2 based.

## Typical use cases

### Brute-force and map a domain's subdomains

Visualise where a target's infrastructure actually sits.

```
dns2geoip -D example.com -w subdomains.txt
```

Outcome: resolves every wordlist entry under `example.com`, reports the
subdomains found, geolocates each IP against
`/usr/share/GeoIP/GeoIPCity.dat`, and writes `output.kml` containing one
placemarker per located subdomain — open it in Google Earth to see the
geographic spread.

### Custom database and output paths

Point it at your own GeoIP snapshot and report file.

```
dns2geoip -D example.com -w subdomains.txt -x /opt/GeoIPCity.dat -o example-map.kml
```

Outcome: same brute-force/geolocation run, but reading the city database
from `/opt/GeoIPCity.dat` (`-x`) and writing the placemarkers to
`example-map.kml` (`-o`) instead of the defaults.

*No corpus evidence — derived from the packaged source (dns2geoip.py
argparse interface, verified against the upstream script).*
