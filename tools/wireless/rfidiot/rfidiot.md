---
name: "rfidiot"
categories:
  - blackarch-wireless
version: "308.8fe8057"
homepage: "http://rfidiot.org/"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-31"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "AdamLaurie/RFIDIOt"
github-stars: 1136
github-pushed: "2026-10-25"
github-archived: false
github-issues: 12
github-match: "search"
gh-checked: "2026-11-07"
worth: 8.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 183
gh-activity-checked: "2026-11-09"
---

# rfidiot

> Adam Laurie's Python library and script collection for probing RFID/NFC — from Mifare and HID prox to ePassports and EMV bank cards.

## What it's for

RFIDIOt ("RFID IO tools") is a Python 3 library plus dozens of ready scripts for exploring contactless technology with a consumer reader (PC/SC readers like the ACR122U by default, libnfc/PN53x via `-R READER_LIBNFC`, and legacy ACG/Frosch serial readers). The scripts cover tag inventory (`readtag.py`), Mifare reading and key brute-forcing, HID/iClass and Hitag2 work, UID emulation and relay, Chameleon device control, ePassport reading and Passive Authentication (`mrpkey.py`, `passiveauth.py`), and full EMV chip-and-PIN walking with offline certificate-chain verification (`ChAP.py`, `rfidiot-cli.py`). Every tool shares the same global reader options (`-h` lists them; `-N` enumerates libnfc readers, `-r 1` picks a PC/SC slot) and does nothing without physical card presence. It is an analysis/research toolkit — great for card auditing and protocol learning, not a turnkey cloner. The 2026 master is the Python 3 port (original Python 2.7 code preserved on a separate branch).

## Typical use cases

### Identify the tag currently on the reader

The universal first step for an unknown card.

```
python3 readtag.py -R READER_PCSC
```

Outcome: selects the tag and prints its type and UID; add `-R READER_LIBNFC -f 0` to target a libnfc device instead, or `-l /dev/ttyUSB0` for the old serial readers — the same reader flags work across every RFIDIOt script.

### Read an ePassport and verify its integrity

Dump the chip's data groups, then authenticate them against the CSCA trust chain.

```
python3 mrpkey.py TEST
sudo python3 passiveauth.py /tmp/EF_SOD.BIN masterlist.ml /tmp
```

Outcome: `mrpkey.py` takes the machine-readable zone's lower line (or `TEST` for sample data) as the BAC key seed and writes each EF_DGxx.BIN plus EF_SOD.BIN under /tmp, displaying the passport's personal and biometric data groups; `passiveauth.py` then verifies the document's cryptographic integrity against a CSCA master list and reports the passport SAFE, TAMPERED or UNTRUSTED.

### Audit a contactless EMV bank card

Walk the card's applications and verify its certificate chain offline.

```
python3 ChAP.py -c
```

Outcome: performs PPSE → AID selection → GPO → record reads, decoding the EMV BER-TLV data (PAN, expiry, CVM rules, cryptogram info) and printing the transaction log with amount/date/currency per entry; `-c` additionally recovers the CA → issuer → ICC certificate chain and checks the card's signature (SDA/DDA/fDDA/CDA), reporting whether the chip's data is genuinely bank-signed — run without a PIN to stay strictly read-only.

### Script a card interaction over one session

Drive multiple low-level commands through the CLI front end.

```
python3 rfidiot-cli.py AID ALL
```

Outcome: executes ISO 7816 AID selection and prints what the card offers, accepting further chained commands in one invocation — the building block for scripted card audits without writing a new tool per protocol.

*No corpus evidence — derived from upstream documentation.*
