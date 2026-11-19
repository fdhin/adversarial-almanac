---
name: "python-frida"
categories:
  - blackarch-reversing
version: "17.7.3"
homepage: "https://pypi.org/project/frida/#files"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-02-21"
build-system: python
license: custom:unknown
aliases: ["python2-frida"]
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# python-frida

> The Frida dynamic-instrumentation engine as a Python module: attach, spawn and script instrumentation from Python code.

## What it's for

This is the `frida` package on PyPI — the Frida core wrapped as an importable Python module, and the programmatic base the [[python-frida-tools]] CLI suite (the `frida` REPL, `frida-trace`, `frida-ps`) is itself built on. A Python script uses it to attach to a local process or connect to a device — a USB Android/iOS handset, a remote host running `frida-server` — create a JavaScript session, inject it, and consume the script's `send()` messages and RPC exports in Python callbacks. Reach for the bindings when instrumentation belongs inside a larger Python workflow (automation, fuzzing harnesses, dumpers such as [[frida-extract]]) instead of an interactive prompt. The injected side is always JavaScript; these bindings own the process, device and script lifecycle. A remote device must already run a matching `frida-server` — the bindings talk to it, they do not install it.

## Typical use cases

### Attach to a running process and inject a script

Instrument a process that is already up.

```python
import frida

session = frida.attach("target")
script = session.create_script("send(Process.arch);")
script.on("message", lambda message, data: print(message))
script.load()
```

Outcome: attaches by process name or pid, compiles the JavaScript source into a session script, wires a Python callback to its messages, and injects it — the `send()` payload prints in the Python process. `script.unload()` and `session.detach()` tear everything down.

### Spawn an app gated, hook it, then resume

Get hooks in place before any target code runs.

```python
device = frida.get_usb_device()
pid = device.spawn(["com.example.app"])
session = device.attach(pid)
script = session.create_script(open("hook.js").read())
script.load()
device.resume(pid)
```

Outcome: spawns the app suspended on the USB device, attaches and loads `hook.js` while the process is still at the spawn gate, then resumes it so the hooks cover first-run initialization. `frida.enumerate_devices()` lists what `get_usb_device()`/`get_remote_device()` can reach.

### Enumerate processes and applications

Discover instrumentable targets on a device.

```python
frida.get_local_device().enumerate_processes()
```

Outcome: returns Process objects (pid, name) for the local machine — the same data `frida-ps` tabulates; a device object additionally offers `enumerate_applications()` for installed mobile apps and `find_process_by_name()` lookups.

*No corpus evidence — derived from upstream documentation.*
