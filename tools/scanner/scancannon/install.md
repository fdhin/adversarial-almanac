---
name: "scancannon-install"
categories:
  - scanner
---

# Installing scancannon

Bash script — clone and make it executable; dependencies are masscan, nmap and standard Unix tools (`dig`, `whois`, `wget`, `awk`, `sed`; `curl` for API detection and webhook notifications):

```bash
git clone https://github.com/johnnyxmas/ScanCannon
cd ScanCannon
chmod +x scancannon.sh
sudo apt install masscan nmap     # dependency install on Debian/Ubuntu
```

Before the first run, edit `scancannon.conf` — set `rate` low to start (default 5000 pps can knock consumer routers over) and review `exclude.txt` for ranges you must not touch. The script prompts to auto-configure the network adapter (source IP/MAC, gateway MAC) for masscan's raw-socket mode on every launch.

Bash 3.2-compatible (Linux, macOS, WSL2); root required. License is CC BY-NC 3.0 — non-commercial use.

Verification: `sudo ./scancannon.sh -c 203.0.113.0/24` against a range you own shows the adapter-configuration prompt and project menu.
