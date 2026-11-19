---
tool: "frida"
build-system: "c"
upstream: "https://github.com/frida/frida"
recipe: "upstream"
license: "see repository"
---

# Installing frida

> Dynamic instrumentation toolkit core: injects a JavaScript runtime into running processes; ships frida-server, frida-gadget and the language bindings.

Source: hacktricks feed; upstream [`frida/frida`](https://github.com/frida/frida).

## Host-side tools (pip)

The recommended way to run Frida from the analysis host is the CLI suite plus
bindings, installed separately from the core repo:

```bash
pip install frida-tools    # CLI tools: frida, frida-ps, frida-trace, ...
pip install frida          # Python bindings
```

See the sibling notes [[python-frida-tools]] and [[python-frida]] — distros
package those directly (e.g. BlackArch `python-frida`, `python-frida-tools`).

## Device-side agent (frida-server / frida-gadget)

Prebuilt binaries for Android, iOS, Windows, macOS and Linux are published on
the [releases](https://github.com/frida/frida/releases) page — download the
archive matching the target OS/architecture (e.g. `frida-server-<version>
-android-arm64.xz`), uncompress, and push it to the device. Gadget builds
(`frida-gadget-<version>-android-arm64.so.xz`, macOS/iOS `.dylib`) come from
the same page.

## Build from source

```bash
git clone https://github.com/frida/frida.git
cd frida
make            # optional: ./configure --prefix=... first
```

Building for Apple OSes additionally needs a code-signing certificate
exported as `MACOS_CERTID` / `IOS_CERTID` etc.

## Not packaged as `frida`

No distro ships the core engine under the name `frida` (checked 2026-11-13);
distro adoption is on the `python-frida` / `python-frida-tools` packages
documented in their own notes.

Backlinks: [[reversing]] · [[frida]]
