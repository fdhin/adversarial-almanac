---
name: "pytbull"
categories:
  - blackarch-scanner
version: "19.3d82a54"
homepage: "https://github.com/netrunn3r/pytbull-ng"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "netrunn3r/pytbull-ng"
github-stars: 31
github-pushed: "2021-06-01"
github-archived: false
github-issues: 23
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pytbull

> pytbull-ng — an IDS/IPS testing framework: roughly 300 attacks in 9 modules, fired from an attacker container at a victim container while your sensor watches.

## What it's for

The Python 3 continuation (Michal Chrobak) of Sébastien Damaye's pytbull: a framework for exercising IDS/IPS deployments. It ships about 300 tests grouped into 9 modules (client-side attacks, bad traffic, brute force, evasion, and so on), and supports writing your own tests and modules using string commands, environment-variable commands, or the original list-based syntax. The deployment model is two Docker containers — a "victim" that starts intentionally vulnerable services (FTP, SSH, web with seeded content) and an "attacker" that runs the test suite against it — with your IDS/IPS inline or monitoring the traffic. The Docker images are the maintained, documented interface; results are collected for review after the run.

## Typical use cases

### Try the framework locally

Stand up victim and attacker on one Docker host.

```
docker run --rm -it efigo/pytbull-ng -m victim
docker run --rm -it efigo/pytbull-ng -m attacker -t 198.51.100.10
```

Outcome: the first container starts the vulnerable services and prints its IP; the second, given that IP via `-t`, executes the test suite against it — a self-contained smoke test with no IDS in line.

### Exercise an inline IDS/IPS

Place the victim where the sensor sees the traffic.

```
docker network create -d macvlan --subnet 198.51.100.0/24 --gateway 198.51.100.1 -o parent=eth0 net_pub
docker run --rm -it --network=net_pub --ip 198.51.100.50 --name=pytbull-ng_victim efigo/pytbull-ng -m victim
docker run -it --rm -p 80:80 --name=pytbull-ng_attacker efigo/pytbull-ng -m attacker -t 198.51.100.50 -l 198.51.100.99
```

Outcome: the victim joins the monitored LAN via macvlan at a fixed address; the attacker container then runs the modules at it, with `-l` stating the attacker host's own IP. Alerts your sensor raises for these known attacks show what it actually detects.

*No corpus evidence — derived from upstream documentation (README and README_docker.md at netrunn3r/pytbull-ng; the `-m victim` / `-m attacker -t <ip> [-l <ip>]` interface verified in the image's `entrypoint.sh`).*
