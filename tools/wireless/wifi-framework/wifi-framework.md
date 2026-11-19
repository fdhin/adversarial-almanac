---
name: "wifi-framework"
categories:
  - wireless
homepage: "https://github.com/domienschepers/wifi-framework"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-13"
github-repo: "domienschepers/wifi-framework"
github-stars: 261
github-archived: false
github-match: "exact"
gh-checked: "2026-11-13"
worth: 5.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
license: "BSD"
---

# wifi-framework

> Research framework that runs Wi-Fi attack/fuzz test cases on top of hostapd and wpa_supplicant, so experiments reuse Linux's entire Wi-Fi stack instead of reimplementing it in Scapy.

## What it's for

Writing a Wi-Fi attack or fuzz test normally means hand-rolling an access point or client in Scapy — beacons, handshakes, retransmissions, ACKs, power-save buffering. wifi-framework (from the researchers behind FragAttacks and the WPA3/queue-frame work) removes that: test cases execute against the real hostap/wpa_supplicant user-space daemons, so the framework broadcasts beacons, performs authentication/association and the WPA2/WPA3 4-way handshake, retransmits injected frames and handles sleeping clients for you. The same test case runs against personal or enterprise configurations unchanged. It is a research/experiment harness — you use it to implement, automate and reproduce attacks (key-reinstallation replay, deauth-countermeasure bypass, fuzzers), not as a turnkey attack tool; it produces test outcomes, not cracked keys. For basic frame-crafting without daemon reuse, plain Scapy (optionally with libwifi) remains the lighter path.

## Typical use cases

### Run a ready-made attack test case against an interface

```
source setup/venv/bin/activate
./run.py wlan1 example-krack-zero-key
```

Outcome: the framework brings up hostap/wpa_supplicant on `wlan1` and executes the named test case (here: an AP that replays 4-way-handshake message 3/4 after the client connects and flags any frame the client encrypts under an all-zero key — the KRACK vulnerability check); `run.py [-h] [--config CONFIG] [--binary BINARY] [--debug DEBUG] iface name` takes the interface and the test's class-defined `name`, not its filename.

### Experiment on simulated radios without physical hardware

```
./setup/setup-hwsim.sh 4
source setup/venv/bin/activate
./run.py wlan1 example-krack-zero-key
./hostap.py wlan2
```

Outcome: four `mac80211_hwsim` virtual Wi-Fi interfaces are created, the test case runs on `wlan1` while `./hostap.py wlan2` starts a client (or with `--ap` an access point) that connects to the test network (`testnetwork` / `passphrase`) — a full over-the-air experiment with zero radio interference, safe on any machine.

### Stand up a plain AP or client daemon for manual testing

```
./hostap.py wlan0 --ap
```

Outcome: a stock hostapd access point (or, without `--ap`, a wpa_supplicant client) is started on `wlan0` using the current network configuration — useful for checking that a test case behaves correctly from the other side, or as a building block in a larger lab setup.

### Switch the whole test run to WPA3 or enterprise auth

```
cd setup && ./load-config.sh wpa3-personal
```

Outcome: the `hostapd.conf`/`supplicant.conf` symbolic links are repointed to the `wpa3-personal` pair (several example configurations ship in `setup/`), so every subsequent test-case run executes against a WPA3-Personal network without touching the test-case code.

### Write an action-based test case

```
class ExampleKrackZerokey(Test):
    name = "example-krack-zero-key"
    kind = Test.Authenticator

    def __init__(self):
        super().__init__([
            Action(trigger=Trigger.Connected, action=Action.Function),
            Action(trigger=Trigger.NoTrigger, action=Action.Receive),
            Action(trigger=Trigger.Received, action=Action.Terminate),
        ])
```

Outcome: a new test case where each `Action` fires when its `Trigger` condition occurs in the connection life-cycle (`AfterAuth`, `Associated`, `Connected`, `Disconnected`, `Received`) — supported actions cover frame `Inject`, custom `Function` callbacks, `Receive` and `Reconnect`; for full control, generic test cases subclass `Daemon`/`Supplicant`/`Authenticator` instead (see `test-4wayhs.py` upstream for a complete 4-way-handshake implementation).

*No corpus evidence — derived from upstream documentation.*
