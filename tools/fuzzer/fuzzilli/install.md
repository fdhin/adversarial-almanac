---
tool: "fuzzilli"
build-system: "make"
upstream: "https://github.com/googleprojectzero/fuzzilli"
recipe: "upstream"
version: "git (2026-11-02)"
license: "see repository"
---

# Installing fuzzilli

> A JavaScript Engine Fuzzer

Source: kitploit directory; upstream [`googleprojectzero/fuzzilli`](https://github.com/googleprojectzero/fuzzilli).

## Prebuilt / releases

Check [upstream releases](https://github.com/googleprojectzero/fuzzilli/releases) for binaries.

## Any other Linux distro

```bash
git clone https://github.com/googleprojectzero/fuzzilli.git
cd fuzzilli
make
sudo make install    # if no install target, copy binary to /usr/local/bin
```

## Not yet packaged

No distro packages this tool yet (checked 2026-11-07).

Backlinks: [[fuzzer]] · [[fuzzilli]]
