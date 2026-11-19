---
name: "wifi_db"
categories:
  - wireless
homepage: "https://github.com/r4ulcl/wifi_db"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-13"
github-repo: "r4ulcl/wifi_db"
github-stars: 145
github-match: "exact"
gh-checked: "2026-11-13"
worth: 5.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
license: "GPL3"
---

# wifi_db

> Turns airodump-ng/Kismet/Wigle capture output into a SQLite database: per-AP RSN/WPA security posture, clients and probes, hashcat-22000 handshakes, 802.1X identities and X.509 certificates.

## What it's for

After a Wi-Fi survey you are left with a pile of `.csv`/`.cap`/`.netxml` capture files that say very little on their own. wifi_db parses them into one queryable SQLite database: every AP with its full RSN/WPA breakdown (WPA version, AKM suites, ciphers, PMF/802.11w state, WPS config, 802.11r/k/v capabilities), connected clients and their probe requests, captured handshakes in hashcat 22000 format, enterprise (MGT) identities and EAP-MD5 pairs, and the X.509 certificates exchanged during EAP-TLS/PEAP/TTLS. It also reveals cloaked SSIDs from probe responses and flags randomized client MACs. It is a post-capture analysis and reporting tool, not a capture tool — you still run airodump-ng or Kismet to produce the input — and it attacks nothing; it structures what you captured so the interesting targets (crackable handshakes, PMF-disabled networks, probe-leaking clients) surface in one query.

## Typical use cases

### Build a database from an airodump-ng survey (Docker)

```
sudo airodump-ng wlan0mon -w scan --manufacturer --wps --gpsd
touch db.SQLITE && chmod a+rw db.SQLITE
docker run -t -v $PWD/db.SQLITE:/app/db.SQLITE -v $CAPTURESFOLDER:/captures/ r4ulcl/wifi_db
```

Outcome: the Docker image parses every capture file in `$CAPTURESFOLDER` into `db.SQLITE` — AP, Client, Probe, Handshake and Certificate tables populated; open the file in sqlitebrowser (or `sqlite3`) to browse. This is the upstream-recommended path because it avoids the tshark/hcxtools dependency chain.

### Parse captures manually into a named database

```
source wifi_db_env/bin/activate
python3 wifi_db.py -d database.sqlite scan-folder
```

Outcome: all capture types found under `scan-folder` (`.csv`, `.kismet.csv`, `.kismet.netxml`, `.log.csv`, wildcards supported) are imported in one pass and appended to `database.sqlite` (`-d` overrides the default `db.SQLITE` name, so separate surveys can share one database).

### Pull crackable hashes out of the survey

```
sqlite3 db.SQLITE 'SELECT * FROM HandshakeAP;'
```

Outcome: one row per captured handshake — AP BSSID/SSID, client MAC and manufacturer, source file, and the hashcat-format (22000) hash line ready to feed to `hashcat -m 22000`; the sibling `EAPMD5` table holds enterprise EAP-MD5 challenge/response pairs for `hashcat -m 4800`.

### Survey the security posture of all seen networks

```
sqlite3 db.SQLITE 'SELECT * FROM SummaryAP;'
```

Outcome: APs grouped by SSID and encryption with AP/client counts, WPA version, PMF state and manufacturers — mixed-security or downgraded SSIDs appear as separate rows, making WPA3/WPA2-transition misconfigurations visible at a glance.

### Sanitize and geo-tag a capture for reporting

```
python3 wifi_db.py -o -t 40.4168 -n -3.7038 scan-01
```

Outcome: builds the database with every MAC/BSSID obfuscated (`AA:BB:CC:XX:XX:XX`-style, safe for demos and conference material) and static GPS coordinates (`-t` lat, `-n` lon) stamped onto sightings where no GPSD data exists.

*No corpus evidence — derived from upstream documentation.*
