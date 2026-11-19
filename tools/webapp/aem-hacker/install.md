---
tool: "aem-hacker"
build-system: "python"
upstream: "https://github.com/0ang3el/aem-hacker"
recipe: "upstream"
license: "MIT"
---

# Installing aem-hacker

> Toolset for hacking Adobe Experience Manager: dispatcher-bypass vulnerability scanning, AEM discovery and SSRF-to-RCE helpers.

Source: howtohunt feed; upstream [`0ang3el/aem-hacker`](https://github.com/0ang3el/aem-hacker).

## From source

Plain Python 3 scripts with no packaging — clone and run:

```bash
git clone https://github.com/0ang3el/aem-hacker.git
cd aem-hacker
python3 aem_hacker.py -u https://aem.example.com --host ATTACKER_IP
```

Dependencies (e.g. `requests`) install with `pip install -r requirements.txt`
if the repo carries one, or manually on ImportError.

## Not packaged

No distro package ships aem-hacker (checked 2026-11-13). A Burp Suite
extension implementing an overlapping check set
(thomashartm/burp-aem-scanner) exists separately and is NOT this tool.

Backlinks: [[windows]] · [[aem-hacker]]
