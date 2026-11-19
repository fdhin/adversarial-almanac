---
tool: "sn1per"
build-system: "node"
upstream: "https://github.com/1N3/Sn1per"
recipe: "blackarch"
source: "https://github.com/1N3/Sn1per"
license: "custom:unknown"
blackarch-pkgver: "723.24368bd"
---

# Installing sn1per

> Automated Pentest Recon Scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sn1per) (version `723.24368bd`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S sn1per    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `nodejs` + `npm`):**

```bash
git clone https://github.com/1N3/Sn1per.git
cd Sn1per
npm install
sudo npm install -g .
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`, `dnspython`, `paramiko`, `requests`, `requests-file`, `tldextract`, `urllib3`
- **Build tools**: `git`, `nodejs`
- **System libraries** (Arch name — translate for your distro):
  - `amap`
  - `android-tools`
  - `arachni`
  - `bash`
  - `bind`
  - `brutex`
  - `cisco-torch`
  - `curl`
  - `cutycapt`
  - `dnsenum`
  - `dnsrecon`
  - `dos2unix`
  - `enum4linux`
  - `firefox`
  - `golismero`
  - `goohak`
  - `hydra`
  - `jq`
  - `metasploit`
  - `nbtscan`
  - `nikto`
  - `nmap`
  - `php`
  - `postgresql`
  - `python`
  - `rpcbind`
  - `ruby`
  - `ruby-mechanize`
  - `ruby-net-http-persistent`
  - `ruby-rake`
  - `ruby-ruby-nmap`
  - `ruby-text-table`
  - `smtp-user-enum`
  - `sqlmap`
  - `sslscan`
  - `sslyze`
  - `testssl.sh`
  - `theharvester`
  - `unzip`
  - `w3af`
  - `wapiti`
  - `whatweb`
  - `whois`
  - `wpscan`
  - `xdg-utils`
  - `xprobe2`
  - `xsstracer`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[sn1per]] · [[BlackArch Tools]]

