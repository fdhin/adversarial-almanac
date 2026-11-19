---
name: "pixiewps"
categories:
  - 802-11
version: "1.4.2-4"
homepage: "https://github.com/wiire/pixiewps"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2024-07-13"
build-system: make
license: GPL3
meta-checked: "2026-11-09"
github-repo: "wiire/pixiewps"
github-stars: 1762
github-pushed: "2026-10-28"
github-archived: false
github-issues: 12
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 6
gh-activity-checked: "2026-11-09"
---

# pixiewps

> Offline WPS PIN brute-forcer (the "Pixie Dust" attack) for routers whose E-S1/E-S2 nonces have too little entropy.

## What it's for

pixiewps exploits low-entropy secrets in some WPS implementations (Ralink, Broadcom, Realtek chipsets chiefly): given the values exchanged in a single WPS M1/M2 transaction — public keys, e-hash1/e-hash2, authkey, e-nonce — it brute-forces the PIN offline in seconds to minutes instead of the hours an online attack such as reaver's takes, and it never alerts the AP because no further WPS transactions are needed. In practice the inputs come from reaver-wps-fork-t6x's `-K`/`-Z` option, which can call pixiewps for you; running it by hand matters when you want to re-run or fine-tune the crack against already-captured values. Since version 1.4 it can also recover the WPA-PSK directly from a passive M1-M7 capture (`--mode 3` with `-7`/`-5`) for some devices. If the target's chipset generates proper random nonces, the attack simply fails — it is a vulnerability-specific shortcut, not a universal WPS breaker.

## Typical use cases

### Run the Pixie Dust attack on captured M1 values

Feed it the six values reaver printed during the WPS exchange.

```
pixiewps -e <pke> -r <pkr> -s <e-hash1> -z <e-hash2> -a <authkey> -n <e-nonce>
```

Outcome: attempts the offline PIN recovery; on success the WPS PIN is printed with a `[+]` marker (exit code 0) — replay it with `reaver -i wlan0mon -b 00:11:22:33:44:55 -p <pin>` to make the AP hand over the WPA passphrase, or pass `-K` to reaver in the first place to have it drive pixiewps automatically.

### Omit the registrar public key after a DH-small session

Exploit the pkr=2 shortcut when the capture came from a `-S` reaver run.

```
pixiewps -e <pke> -s <e-hash1> -z <e-hash2> -a <authkey> -n <e-nonce> -m <r-nonce> -b 00:11:22:33:44:55
```

Outcome: when reaver ran with small DH keys the enrollee public key `-r` is always 2, so pixiewps derives it internally instead of you supplying it — with `-m` (registrar nonce) and `-b` (BSSID) given it can also compute the session keys itself; success and failure reporting are as above.

### Recover the WPA-PSK from a passive capture

Crack a sniffed push-button (PBC) session without ever talking to the AP.

```
pixiewps --mode 3 -e <pke> -r <pkr> -s <e-hash1> -z <e-hash2> -a <authkey> -n <e-nonce> -7 <m7-enc> --start 01/2020 --end 12/2024
```

Outcome: mode 3 assumes the vulnerable nonces derive from time, so restricting `--start`/`--end` to the capture window bounds the search; `-7` supplies the encrypted-settings attribute from M7, and a success prints both the WPS PIN and the WPA-PSK recovered from the encrypted configuration.

*No corpus evidence — derived from upstream documentation.*
