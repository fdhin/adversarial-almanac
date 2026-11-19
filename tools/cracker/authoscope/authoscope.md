---
name: "authoscope"
categories:
  - cracker
homepage: "https://github.com/kpcyrd/authoscope"
github-repo: "kpcyrd/authoscope"
github-match: "exact"
origin: "awesome-pentest"
recipe: "upstream"
license: GPL3
github-stars: 423
github-commits-year: 15
gh-checked: "2026-11-14"
worth: 6.4
worth-basis: "full (activity+popularity+recency+adoption)"
packaged-by: [arch, nixpkgs]
---

# authoscope

> Scriptable network authentication cracker — describe any login flow in a Lua `verify(user, password)` function and get concurrency, progress and reporting for free.

## What it's for

authoscope bruteforces credentials against services the stock crackers don't know: instead of built-in protocol modules you write a short Lua script per service — an HTTP form with a CSRF token, an LDAP bind, a MySQL login, a raw socket protocol — exposing `verify(user, password)`, and the Rust runtime supplies threading, retries, a live progress bar, and result reporting. hydra/medusa/ncrack already cover common protocols; authoscope wins exactly where you would otherwise hand-roll a throwaway Python script. The embedded runtime gives scripts HTTP(S) sessions with cookie handling and HTML/JSON parsing, LDAP (direct bind and search-then-bind), MySQL clients, raw TCP helpers, and hashing/HMAC primitives, so challenge-response logins stay expressible. Dictionary, credential-stuffing (combolist) and user-enumeration attack modes drive it from users/passwords lists; errored attempts are requeued rather than silently dropped. GPLv3, by the author of sn0int; packaged in Arch and nixpkgs.

No corpus evidence — derived from upstream documentation. CLI verified against `src/args.rs`: the README's legacy `authoscope oneshot` debug subcommand is `run` in the current source.

## Typical use cases

### Dictionary attack against a scripted service

```
authoscope dict users.txt passwords.txt scripts/example.lua -o found.txt
```

Outcome: Submits the users × passwords cross product through the script's verify() to the thread pool; valid credentials stream to the terminal and are appended to found.txt as `script:user:password` lines.

### Credential stuffing with a combolist

```
authoscope combo breach-combo.txt scripts/http.lua -n 32
```

Outcome: Tests each `user:password` pair from the combolist against the service defined in scripts/http.lua, with 32 concurrent workers instead of the default 16.

### Enumerate valid usernames

```
authoscope enum users.txt scripts/mail-login.lua
```

Outcome: Runs each user through verify() with no password — the script decides what "account exists" looks like — and prints the usernames reported valid.

### Test one credential pair while debugging a script

```
authoscope run scripts/http.lua user 'password' -x
```

Outcome: A single verify() execution with print() output visible for debugging; -x makes invalid credentials exit with code 2, so the check itself can drive scripts or CI.

### Validate list files before a run

```
authoscope fsck -c combolist.txt users.txt
```

Outcome: Reports which lines of the given files are malformed (encoding or formatting); -c enforces exactly one colon per line for combolist inputs.

### Script a custom login flow

```
descr = "example.com"

function verify(user, password)
    session = http_mksession()

    req = http_request(session, 'GET', 'https://example.com/login', {})
    resp = http_send(req)
    if last_err() then return end

    csrf = html_select(resp['text'], 'input[name="csrf"]')

    req = http_request(session, 'POST', 'https://example.com/login', {
        form={ user=user, password=password, csrf=csrf["attrs"]["value"] }
    })
    resp = http_send(req)
    if last_err() then return end

    return resp['text']:find('Login successful') ~= nil
end
```

Outcome: A drop-in service script: fetch the login page, parse the CSRF token from the HTML, submit the form with the candidate credentials, and declare success on the response text — save as scripts/example.lua and point any attack subcommand at it.
