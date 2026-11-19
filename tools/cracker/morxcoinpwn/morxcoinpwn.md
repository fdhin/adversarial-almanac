---
name: "morxcoinpwn"
categories:
  - blackarch-cracker
version: "1.0"
homepage: "http://www.morxploit.com/tools/"
packaged-by: [blackarch]
blackarch-builddate: "2018-10-03"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# morxcoinpwn

> Mass-checks passphrase lists for funded Bitcoin brainwallets and sweeps hits into a wallet.

## What it's for

MorXCoinPwn is the mass companion to `morxbtcrack`: instead of matching one address, it walks an entire passphrase list, derives each brainwallet's private key and address (via the `keyfmt` helper, packaged separately as `morxkeyfmt`), looks the address up through a block-explorer API, and when it holds coins imports the private key into `bitcoind` over RPC. It was written to demonstrate how quickly weak brainwallet passphrases get found and swept. The hardcoded block-explorer and blockchain RPC endpoints are 2013-era and no longer respond, and the wallet credentials must be edited into the script before use, so today it mainly serves as a working reference for the derive-check-import pattern.

## Typical use cases

### Check a passphrase list for currently funded brainwallets

Run the list with the balance method.

```
morxcoinpwn passphrases.txt addressbalance
```

Outcome: prints a running line counter as it derives each address; for any address with a positive balance it prints `Got => <address> - Balance: <amount>` and immediately runs `bitcoind importprivkey` to sweep the key into the configured wallet.

### Catch brainwallets that were ever funded

Use the received-coins method instead of current balance.

```
morxcoinpwn passphrases.txt getreceivedbyaddress
```

Outcome: flags addresses that have ever received coins, including already-drained ones — useful for auditing how many passphrases in a list were actually used as brainwallets, not just which ones still hold funds.

*No corpus evidence — derived from upstream documentation.*
