---
name: "cryptohazemultiforcer"
categories:
  - blackarch-cracker
version: "1.31a"
homepage: "http://www.cryptohaze.com/multiforcer.php"
packaged-by: [blackarch]
blackarch-builddate: "2016-06-15"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# cryptohazemultiforcer

> CUDA brute-forcer built for very large unsalted hash lists — per-position character sets, lengths 0–14, and multi-host network clustering.

## What it's for

The Cryptohaze Multiforcer is a GPU brute forcer aimed at cracking whole password databases at once: its stepping rate barely degrades between a 10-hash and a 1.4-million-hash list, so auditing an entire dumped account table costs almost the same as one hash. It covers MD5, NTLM, MD4, SHA1, LM, MSSQL, salted variants (`MD5_PS`, `MD5_SP`, `SSHA`), and chained forms like `DOUBLEMD5`/`TRIPLEMD5`. Attacks are charset-driven brute force (single or per-position character sets), not dictionary or rule attacks, and it requires an NVIDIA CUDA GPU (8000 series or better); it does not run on AMD cards. Hashes go in one per line as ASCII hex. The upstream project went dormant around 2015 — for maintained GPU cracking use hashcat or John the Ripper's OpenCL formats.

## Typical use cases

### Brute-force an MD5 list with a single charset

```
cryptohazemultiforcer -h MD5 -f hashes.txt -c charset.txt --min 1 --max 8
```

Outcome: walks every candidate of length 1–8 composed of the characters in charset.txt (one line of allowed characters) against the whole hash file on the GPU; cracked pairs print to the console and append to the output file given with `-o`.

### Use a different charset per password position

```
cryptohazemultiforcer -h NTLM -f ntlm.txt -u positions.txt --min 8 --max 8 -o found.txt
```

Outcome: line 1 of positions.txt constrains the first character, line 2 the second, and so on — the classic pattern for known-format passwords (e.g. first and last character printable, middle six lowercase); recovered plaintexts accumulate in found.txt, appended rather than overwritten.

### Distribute a run across several machines

```
cryptohazemultiforcer -h MD5 -f hashes.txt -c charset.txt --min 1 --max 8 --enableserver --serveronly
cryptohazemultiforcer --remoteip 198.51.100.10
```

Outcome: the first command starts the coordinating server without local GPU work; each additional host runs the second command and joins the same keyspace, with all results consolidated on the server. Clients can connect and disconnect freely.

### Squeeze maximum speed from a headless box

```
cryptohazemultiforcer -h MD5 -f hashes.txt -c charset.txt --min 1 --max 6 --threads 512 --blocks 512 -m 500
```

Outcome: raises GPU kernel occupancy and kernel time (default 50 ms) for the last few percent of throughput; the desktop display becomes nearly unusable while it runs, so this is for dedicated cracking hosts — drop to `--threads 256` if kernels fail to launch on smaller cards.

*No corpus evidence — derived from upstream documentation.*
