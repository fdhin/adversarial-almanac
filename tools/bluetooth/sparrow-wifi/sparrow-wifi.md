---
name: "sparrow-wifi"
categories:
  - blackarch-bluetooth
version: "v2.0.r31.gb4f4592"
homepage: "https://github.com/ghostop14/sparrow-wifi"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-25"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "ghostop14/sparrow-wifi"
github-stars: 1644
github-pushed: "2026-10-21"
github-archived: false
github-issues: 56
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 196
gh-activity-checked: "2026-11-09"
---

# sparrow-wifi

> PyQt5 WiFi and Bluetooth/BLE analyzer with a headless REST agent, GPS wardriving, SDR spectrum overlays, and drone RemoteID detection.

## What it's for

A single Python 3 platform for wireless situational awareness on Linux: 2.4/5 GHz WiFi surveys (SSID discovery, signal strength, channel utilization), Bluetooth Low Energy advertisement scanning and iBeacon advertising, promiscuous classic+LE discovery via Ubertooth, real-time spectrum overlays from HackRF or Ubertooth, GPS-tagged mapping and wardriving, and a hunt mode with telemetry windows for physically locating emitters. The desktop GUI (`sparrow-wifi.py`) and the headless agent (`sparrowwifiagent.py`) expose the same engines; the agent speaks JSON REST so sensors can run on Raspberry Pis, drones, or rovers and be consumed by the GUI, scripts, or the optional Elasticsearch/OpenSearch bridge (ECS 8.17). A companion web app decodes FAA RemoteID drone broadcasts over WiFi and BLE. Root is required. It is measurement-first: the only active/offensive capability is the optional aircrack-ng "Falcon" plugin (monitor mode, hidden-SSID discovery, deauth, WEP/WPA capture).

## Typical use cases

### Launch the analyzer GUI

```
sudo sparrow-wifi
```

Outcome: opens the PyQt5 interface (upstream equivalent: `sudo ./sparrow-wifi.py` from the source tree). Select a wireless interface and start WiFi and/or BLE scans; results accumulate in channel and vendor views, hunt mode adds high-rate telemetry for locating a chosen emitter, and GPS/Map panels plot findings when gpsd is running. Sessions export to CSV/JSON.

### Deploy a headless remote sensor

```
sudo ./sparrowwifiagent.py --port 8020 --allowedips 198.51.100.10 --sendannounce --recordinterface wlan0
```

Outcome: starts the agent listening on port 8020, restricted to the given client IP, announcing itself via UDP broadcast so the GUI can find it, and auto-recording scans on `wlan0` from startup. On a Raspberry Pi add `--userpileds` for status LEDs, `--mavlinkgps 3dr` to pull GPS from a drone, or `--staticcoord LAT,LON,ALT` when no GPS receiver is attached.

### Pull scan results from the agent's REST API

```
curl http://sensor.example.com:8020/wireless/networks/wlan0
```

Outcome: returns a JSON document with the networks seen on the sensor's `wlan0` (add `?frequencies=2412,2437,2462` to filter). Related endpoints: `/wireless/interfaces` lists scannable interfaces, `/gps/status` reports GPS state, `/bluetooth/discoverystarta` starts a BLE advertisement scan and `/bluetooth/discoverystatus` returns its results — enough to script distributed surveys or feed a SIEM via the Elasticsearch bridge.

### Monitor for drone RemoteID broadcasts

```
sudo python3 sparrow_droneid/app.py
```

Outcome: starts the DroneID web application; open `http://localhost:8097`, configure a monitor-mode WiFi interface (and optionally a BLE adapter), and detected drones appear on a live map with serial/operator ID, altitude, and speed, alongside alerting, geofence overlays, and history/replay. Requires a monitor-mode-capable adapter such as rtl8812au for WiFi capture.

*No corpus evidence — derived from upstream documentation.*
