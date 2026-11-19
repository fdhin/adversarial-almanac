---
tool: "pymobiledevice3"
build-system: "python"
upstream: "https://github.com/doronz88/pymobiledevice3"
recipe: "upstream"
license: "GPL3"
---

# Installing pymobiledevice3

> Pure-Python iOS device interaction: lockdownd services, syslog, crash reports, pcap capture, AFC, backups, profiles — CLI and Python API.

Source: HackTricks feed admission; upstream [`doronz88/pymobiledevice3`](https://github.com/doronz88/pymobiledevice3).

## PyPI (official channel)

PyPI is the only official distribution. The recommended installer is `uv`:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh   # macOS / Linux
uv tool install --managed-python pymobiledevice3
```

`pipx install pymobiledevice3` works too, and `pip install pymobiledevice3`
inside a project virtualenv for the Python API. One-off use without installing:
`uvx --managed-python pymobiledevice3 usbmux list`.

## Platform prerequisites

- **Linux**: `usbmuxd` (and `libusbmuxd`/`libplist`) must be running for USB devices.
- **Windows**: iTunes (or Apple Devices app) for the usbmuxd stack.
- **macOS**: nothing extra — usbmuxd ships with the OS.

Verify connectivity with `pymobiledevice3 usbmux list` (device connected and unlocked).

## Notes

- Third-party distro packages (e.g. AUR `python-pymobiledevice3`) exist but are
  unmaintained by upstream and may lag.
- iOS 17+ developer services need a tunnel; the CLI builds it automatically.

Full install guide: <https://doronz88.github.io/pymobiledevice3/installation/>.

Backlinks: [[mobile]] · [[pymobiledevice3]]
