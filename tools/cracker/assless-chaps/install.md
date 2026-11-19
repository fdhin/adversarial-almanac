---
tool: "assless-chaps"
build-system: "rust"
upstream: "https://github.com/sensepost/assless-chaps"
recipe: "upstream"
license: "see repository"
---

# Installing assless-chaps

> Recovers the NT hash behind an MSCHAPv2/NTLMv1 challenge-response in milliseconds by splitting the DES keyspace and looking the rest up in a hash database.

Source: hacktricks feed; upstream [`sensepost/assless-chaps`](https://github.com/sensepost/assless-chaps).

## Build the Rust version from source

Requires cargo and SQLite ≥ 3.6.8:

```bash
git clone https://github.com/sensepost/assless-chaps.git
cd assless-chaps/assless-chaps-rs
cargo build --release
# binary: target/release/assless-chaps
```

## Python version

Requires `python3`, `sqlite3` CLI and `pycryptodome`:

```bash
git clone https://github.com/sensepost/assless-chaps.git
cd assless-chaps
python3 assless-chaps.py <Challenge> <Response> hashes.db
```

## Companion scripts

`mksqlitedb.py`, `nthash-from-clear.py` and `ntlm-ssp.py` ship in the
same repo (python3 + sqlite3).

## Not yet packaged

No distro packages this tool (checked 2026-11-13) — build or run from
source.

Backlinks: [[cracker]] · [[assless-chaps]]
