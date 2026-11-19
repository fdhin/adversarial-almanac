---
name: "jcrack"
categories:
  - blackarch-wireless
version: "0.3.6"
homepage: "http://www.thedrahos.net/jcrack/"
packaged-by: [blackarch]
blackarch-builddate: "2017-04-08"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "jeremydrahos/jcrack"
github-stars: 0
github-pushed: "2017-04-06"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# jcrack

> Generates default-password dictionaries for specific consumer wireless gateways.

## What it's for

jcrack ("Jiggy Crack", by Jeremy Drahos) is a small interactive shell
script that builds wordlists for routers whose factory WPA keys follow
a known pattern derived from the SSID. It covers a fixed menu of models
(Arris DG860A/TG1672G/TG862G/DG1670A, Belkin units with belkin.xxx-style
SSIDs, Motorola SurfBoard SBG6580, Ubee DDW365/DVW3201B, Technicolor
TC8717T/TC8715D). For the chosen model it asks for the last characters
of the SSID and calls crunch to emit every key matching the vendor's
pattern. It does not capture handshakes or crack anything itself — it
only produces the dictionary, which you then feed to aircrack-ng,
hashcat or a similar tool against a captured handshake.

## Typical use cases

### Generate a default-password dictionary

Produce a wordlist for one specific router model.

```
jcrack
```

Outcome: an interactive menu of supported models; pick one, answer the
prompt for the last two characters of the SSID, and crunch writes a
dictionary file such as `ddw365.ab` in the current directory — test the
candidates with `aircrack-ng -w ddw365.ab capture.cap` against a
captured handshake.

*No corpus evidence — derived from upstream documentation.*
