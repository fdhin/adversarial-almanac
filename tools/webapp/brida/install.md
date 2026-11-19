---
name: "brida-install"
categories:
  - webapp
---

# Installing brida

Prerequisites (Brida ≥ 0.6): Burp Suite (Community or Pro), NodeJS + npm on PATH, and the Python side:

```bash
pip install frida frida-tools pyro4
```

plus a jailbroken/rooted device with frida-server running (or an APK patched with frida-gadget). Brida ≥ 0.6 needs Frida ≥ 17; for older Frida versions use release 0.6pre.

Load the extension:

```
Burp → Extender → BApp Store → Brida, Burp to Frida bridge → Install
```

or grab the latest JAR from the [GitHub releases](https://github.com/federicodotta/Brida/releases) and add it under `Extender → Extensions → Add`. Building from source uses Gradle (or Maven with the patched RSyntaxTextArea in `libs/`).
