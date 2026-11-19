---
tool: "odat"
build-system: "python"
upstream: "https://github.com/quentinhardy/odat"
recipe: "upstream"
license: "custom:unknown"
---

# Installing odat

> ODAT — Oracle Database Attacking Tool: penetration-testing tool for remote Oracle databases.

Source: hacktricks feed; upstream [`quentinhardy/odat`](https://github.com/quentinhardy/odat).

## Prebuilt standalone (recommended)

```
# https://github.com/quentinhardy/odat/releases/
tar xzf odat_linux.tar.gz && ./odat_libs/odat -h
```

Outcome: the pyinstaller standalone bundles every dependency (Instant Client included) and runs on Linux with no Oracle installation; `-h` prints the module list.

## Development version from source

The dev version needs Oracle Instant Client (basic + SDK) and cx_Oracle on an Ubuntu-class system:

```
sudo apt-get install libaio1 python3-dev alien python3-pip python3-scapy
# convert + install the Oracle instantclient RPMs (basic + devel) with alien, then:
export ORACLE_HOME=/usr/lib/oracle/19.3/client64/
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$ORACLE_HOME/lib
sudo pip3 install cx_Oracle colorlog termcolor pycrypto passlib python-libnmap argcomplete
git clone https://github.com/quentinhardy/odat.git && cd odat
./odat.py -h
```

Outcome: `./odat.py -h` prints usage with no import errors; upstream recommends the `master-python3` branch and documents the full Instant Client install (env vars, `/etc/ld.so.conf.d/oracle.conf`, `ldconfig`) in its README.

## Notes

- No distro packages this tool (checked 2026-11-13).
- Upstream ships no license file in the repository (checked 2026-11-13).
- Docker build files for target simulation ship in the repo's `Docker/` directory.

Backlinks: [[database]] · [[odat]]
