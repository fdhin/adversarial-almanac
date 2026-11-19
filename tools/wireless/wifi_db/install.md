---
tool: "wifi_db"
build-system: "python"
upstream: "https://github.com/r4ulcl/wifi_db"
recipe: "upstream"
license: "GPL3"
---

# Installing wifi_db

> Parses airodump-ng/Kismet/Wigle captures into a SQLite database of APs, clients, handshakes (hashcat 22000) and 802.1X identities.

Source: HackTricks feed admission; upstream [`r4ulcl/wifi_db`](https://github.com/r4ulcl/wifi_db).

## Docker (recommended)

```bash
docker pull r4ulcl/wifi_db
docker run -t -v $PWD/db.SQLITE:/app/db.SQLITE -v $CAPTURESFOLDER:/captures/ r4ulcl/wifi_db
```

The image bundles tshark and hcxtools, avoiding the local dependency build.

## Manual installation (Debian-based: Kali, Parrot, Ubuntu)

Dependencies: `python3`, `python3-pip`, `tshark`, [hcxtools](https://github.com/ZerBea/hcxtools).

```bash
sudo apt install tshark python3 python3-pip
sudo apt install pkg-config libcurl4-openssl-dev libssl-dev zlib1g-dev make gcc
git clone https://github.com/ZerBea/hcxtools.git && cd hcxtools && make && sudo make install && cd ..
git clone https://github.com/r4ulcl/wifi_db && cd wifi_db
sudo apt install python3-venv
python3 -m venv wifi_db_env
source wifi_db_env/bin/activate
pip3 install -r requirements.txt
python3 wifi_db.py -h
```

The venv must be re-activated (`source wifi_db_env/bin/activate`) in every new shell before running `wifi_db.py`.

## Arch

```bash
sudo pacman -S wireshark-qt python-pip python
```

Then build hcxtools and the venv exactly as above.

## Not yet packaged

No mainstream distro packages wifi_db — use the Docker image or the venv install. Checked 2026-11-13.

Backlinks: [[wireless]] · [[wifi_db]]
