---
tool: "hbctool"
build-system: "python"
upstream: "https://github.com/bongtrop/hbctool"
recipe: "upstream"
license: "MIT"
---

# Installing hbctool

> CLI for disassembling and assembling Hermes bytecode (React Native Android apps).

Source: HackTricks feed admission; upstream [`bongtrop/hbctool`](https://github.com/bongtrop/hbctool).

## PyPI

```
pip install hbctool
```

## From source

```bash
git clone https://github.com/bongtrop/hbctool.git
cd hbctool
python test.py        # run unit tests before submitting changes
poetry install && poetry build
pip install --force-reinstall dist/hbctool-<VERSION>-py3-none-any.whl
```

Requires Python 3. On Android, the target HBC file is normally
`assets/index.android.bundle` inside the APK (extract with apktool/unzip).

## Not yet packaged

No distro packages (Arch/BlackArch/Kali/AUR/nixpkgs) as of 2026-11-13 —
PyPI or source only.

Backlinks: [[mobile]] · [[hbctool]]
