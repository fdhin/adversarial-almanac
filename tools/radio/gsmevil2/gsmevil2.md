---
name: "gsmevil2"
categories:
  - blackarch-radio
version: "18.ce64fa5"
homepage: "https://github.com/ninjhacks/gsmevil2"
packaged-by: [blackarch]
blackarch-builddate: "2026-05-02"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "ninjhacks/gsmevil2"
github-stars: 372
github-pushed: "2026-04-28"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 6
gh-activity-checked: "2026-11-09"
---

# gsmevil2

> Web dashboard that turns a gr-gsm receiver into an IMSI/SMS capture browser.

## What it's for

Watching GSM traffic without touching Wireshark: GSMEvil 2 is a Python/Flask web application that listens for the GSMTAP packets produced by gr-gsm's `grgsm_livemon` on a network interface and presents captured IMSI numbers, SMS, and cell information in a browser dashboard with live updates. It is a passive monitor of an existing downlink capture chain — it does not tune the SDR, find frequencies, or work without gr-gsm; the standard setup is kalibrate-rtl (or `grgsm_scanner`) to find a BTS frequency, `grgsm_livemon` to feed packets to the loopback interface, then GSMEvil 2 on top. Capturing traffic on networks you do not own is illegal in most jurisdictions; the tool was written for learning how GSM works.

## Typical use cases

### Start the capture dashboard with defaults

Bring up the web UI against a running `grgsm_livemon` (which sends its GSMTAP stream to `lo`).

```
sudo gsmevil2
```

Outcome: a Flask server starts on `localhost` port 80 and a raw-packet listener opens on the `lo` interface (root or `setcap` is required for raw capture). Browsing to `http://localhost/` shows a live dashboard; each IMSI seen in paging/assignment traffic and each SMS decoded from the monitored C0 channel appears as it is received, with timestamps and cell context. Run `grgsm_livemon -f <downlink-frequency>M` in another terminal — no dashboard entries appear until it is streaming.

### Listen on a different interface and port

Point the capture at another interface (e.g. when the receiver runs on a remote host sending UDP to this one) and move the web UI off port 80.

```
sudo gsmevil2 -i eth0 -p 8080
```

Outcome: the dashboard serves on port 8080 and GSMTAP packets are read from `eth0` instead of loopback — connect `grgsm_livemon`'s UDP output to that host and the same IMSI/SMS view populates. `-i`/`--iface` and `-p`/`--port` set the two sides independently; `--host` binds the web server address.

*No corpus evidence — derived from upstream documentation.*
