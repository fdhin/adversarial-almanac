---
name: "snoopy-ng"
categories:
  - blackarch-drone
version: "128.eac73f5"
homepage: "https://github.com/sensepost/snoopy-ng"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
github-repo: "sensepost/snoopy-ng"
github-stars: 444
github-pushed: "2017-03-05"
github-archived: false
github-issues: 43
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# snoopy-ng

> SensePost's distributed tracking framework: plugin-driven "drones" passively log Wi-Fi clients and probes with GPS context, sync to a central server, and feed Maltego for movement analysis.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Snoopy NG (sensepost/snoopy-ng, SensePost, version 2.0) is a distributed sensor framework for physically tracking people through their devices. Each instance ("drone" — typically a BeagleBone/Raspberry Pi with a monitor-mode Wi-Fi card and GPS) runs any combination of Python plugins that passively collect data: Wi-Fi clients and their probed SSIDs, GPS fixes, system health, cookies via sslstrip, and more. Everything is written to a local database (SQLite by default, any SQLAlchemy URL supported), and clients sync their tables to a central server over HTTP, or pull replicas back from it — so one server aggregates observations from many distributed sensors and a laptop can retrieve the merged picture. Exploration is done by importing the bundled Maltego entity/transform pack, which pivots drone → location → client → observation. It is a passive collection and analysis platform — it does not crack or attack wireless networks — and this is NOT the same project as the various same-named WLAN "snoopy" tools. License is non-commercial use only. Packaged launchers: `snoopy` (main tool) and `snoopy-auth` (account/key manager; the upstream README spells it `snoopy_auth`).

## Typical use cases

### Discover plugins and their parameters

See what this build can collect before deploying a sensor.

```
snoopy --list
snoopy -ii -m wifi
```

Outcome: `--list` (short `-i`) prints every installed plugin with the key=value parameters it accepts; `-ii`/`-iii` add progressively more detail, and `-i -m wifi` restricts the listing to one plugin. `snoopy --help` shows the global flags (`-d` drone name, `-l` location, `-s` server URL, `-k` key, `-f` flush, `--dbms`).

### Capture Wi-Fi observations locally

Run a standalone sensor that logs everything to its own SQLite file.

```
snoopy -v -m wifi:mon=True -m sysinfo -m heartbeat -d myDrone -l London
```

Outcome: puts the wireless card into monitor mode and passively records observed clients and probes (plus system info and periodic heartbeats) into `snoopy.db` in the working directory, all rows tagged as drone `myDrone` at location `London`; `-v` prints each plugin's startup and collection activity.

### Sync a fleet of drones to a central server

Aggregate many sensors' observations over HTTP.

```
snoopy-auth --create myDrone01      # prints the sync key for this drone name
snoopy -v -m server                 # on the server host
snoopy -v -m wifi:mon=True -s http://198.51.100.10:9001/ -d myDrone01 -l London -k KEY
```

Outcome: `snoopy-auth --create` prints a per-drone key; the server plugin listens on `0.0.0.0:9001` and logs caught data per table; the client periodically syncs its tables upstream (`Snoopy successfully sunc N elements over N tables`). Adding `-f` flushes local rows once synced.

### Pull a replica for analysis

Retrieve the server's merged dataset onto an analyst machine.

```
snoopy -m local_sync:server_url=http://198.51.100.10:9001/ -d myDrone01 -k KEY
```

Outcome: prints `Plugin local_sync pulled N records from remote server` and writes them into the local database, giving Maltego (after importing `snoopy_entities.mtz` from the transforms folder) a full drone → location → client → observation graph to mine.

### Enrich captured SSIDs with WiGLE geo-location

Have the server resolve observed networks to coordinates.

```
snoopy -m server -m wigle:username=u,password=p,email=user@example.com
```

Outcome: alongside catching drone syncs on port 9001, the wigle plugin looks observed SSIDs up against the WiGLE database using the supplied account, adding geo-location data the Maltego transforms use to map where a device has been.

### Store captures on removable media

Keep the database off the sensor's SD card.

```
snoopy -v -m wifi:mon=True --dbms=sqlite:////media/USB01/snoopy.db
```

Outcome: prints `Capturing local only. Saving to 'sqlite:////media/USB01/snoopy.db'` and writes all plugin tables there instead of `./snoopy.db`; any SQLAlchemy URL works the same way, e.g. `--dbms=mysql://user:***@localhost/snoopy_db` on a server.
