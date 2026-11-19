---
name: "aztarna"
categories:
  - blackarch-recon
version: "1.2.1"
homepage: "https://github.com/aliasrobotics/aztarna"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: python
license: GPL3
meta-checked: "2026-11-09"
github-repo: "aliasrobotics/aztarna"
github-stars: 92
github-pushed: "2026-07-14"
github-archived: true
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# aztarna

> Footprint robot systems (ROS, SROS, ROS 2) and industrial routers across a network range.

## What it's for

Aztarna (Basque for "footprint"), by Alias Robotics, scans for exposed Robot Operating System instances and industrial routers. For ROS masters it enumerates nodes, published/subscribed topics, services, parameters and active communications; for SROS it also reports the node allow/deny policies; for ROS 2 it walks the DDS communication domains; for industrial routers (eWON, Moxa, Sierra Wireless, Westermo) it detects the device and checks default credentials. It is a defensive/assessment tool for robot security — the project is archived upstream and successor work moved to Alias Robotics' alurity framework, but the CLI remains packaged and functional.

## Typical use cases

### Scan a subnet for ROS masters

```
aztarna -t ROS -p 11311 -a 198.51.100.0/24
```

Outcome: finds live ROS masters on the default port 11311 across the range and prints each system's nodes, topics, services, parameters and communications — a full map of the exposed robot middleware. Port accepts single (`11311`), range (`11311-11500`) or list (`11311,11312,11313`) forms.

### Scan a single host from a target file

```
aztarna -t ROS -p 11311 -i targets.txt -o ros_results.txt
```

Outcome: reads addresses from the input file instead of a range argument and writes the findings to the named output file for later diffing.

### Extended scan for SROS policies

```
aztarna -t SROS -e -p 11311 -a 198.51.100.10
```

Outcome: detects whether the host runs a SROS master (and whether it uses the demo configuration), and with `-e` reports each node's allow/deny policies — publishable topics, executable services, readable parameters.

### Survey ROS 2 domains

```
aztarna -t ROS2
```

Outcome: sweeps the default DDS domain range (0-231) and lists the nodes, topics and services present in each communication domain; add `-d 15` to restrict to `ROS_DOMAIN_ID=15`.

### Find industrial routers with default credentials

```
aztarna -t IROUTERS -a 198.51.100.0/24
```

Outcome: identifies eWON, Moxa, Sierra Wireless and Westermo routers in the range and reports which ones still accept default credentials.

*No corpus evidence — derived from upstream documentation.*
