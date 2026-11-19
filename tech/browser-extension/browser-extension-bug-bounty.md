---
technology: "Browser extensions (Chrome / Firefox add-on security surface)"
kind: bug-bounty-methodology
audience: ai-agent
status: upstream-only
family: web-platforms
version: "1.0"
provenance: "upstream-only (pentest-book + primary sources); zero corpus evidence by design — owner-approved new-unit wave (PB-INGEST) 2026-11-13; manifest semantics and MV3 behaviors verified against Google's primary developer documentation (service-worker background, declarativeNetRequest replacing blocking webRequest, remote-code and arbitrary-string-execution ban). Zero CVEs by design"
source: "six2dez/pentest-book (others/browser-extension-security.md, no license — rewritten with attribution), accessed 2026-11-13"
---

# Browser extension audit methodology

Companion overview: [[browser-extension]].

Scope: auditing a specific extension (Chrome or Firefox) for the capability and trust-model failures that turn it into an implant — excessive grants, broken message-boundary validation, privileged sinks reachable from web content, and native-messaging escalation. Decision inputs at every phase: is this grant justified, is this boundary validated, is this sink reachable.

## Phase 0 — Acquisition

Enumerate what exists, then get the code on disk for review.

```bash
# Store catalogs
# Chrome Web Store:  https://chrome.google.com/webstore/category/extensions
# Firefox Add-ons:   https://addons.mozilla.org/en-US/firefox/extensions/

# Enterprise force-install inventory (pinned by extension ID — the org's implant list)
reg query "HKLM\SOFTWARE\Policies\Google\Chrome\ExtensionInstallForcelist"
reg query "HKLM\SOFTWARE\Policies\Mozilla\Firefox\Extensions"
defaults read com.google.Chrome ExtensionInstallForcelist    # macOS

# On-disk copies of installed extensions
# Windows: %LOCALAPPDATA%\Google\Chrome\User Data\Default\Extensions\
# macOS:   ~/Library/Application Support/Google/Chrome/Default/Extensions/
# Linux:   ~/.config/google-chrome/Default/Extensions/
# Firefox: ~/.mozilla/firefox/PROFILE/extensions/

# Pull a Chrome extension by ID without installing it (CRX redirect endpoint)
curl -o extension.crx "https://clients2.google.com/service/update2/crx?response=redirect&prodversion=100.0&x=id%3DEXTENSION_ID%26installsource%3Dondemand%26uc"

unzip extension.crx -d extension_unpacked/    # CRX is a wrapped ZIP
unzip extension.xpi -d extension_unpacked/    # Firefox XPI is just a ZIP
```

First read of `extension_unpacked/manifest.json`: `manifest_version` (2 or 3 decides which decade of techniques applies), `permissions` + `host_permissions`, `content_scripts[].matches`, `web_accessible_resources`, `externally_connectable`, `content_security_policy`.

## Phase 1 — Static analysis

Grade the manifest before reading code — the capability model is the finding half the time.

High-signal grants and what they mean:

| Grant | What it buys an attacker |
|---|---|
| `<all_urls>` (permissions or matches) | Every page the victim visits: DOM, forms, sessions |
| `webRequest` + `webRequestBlocking` (MV2) | Read/modify all traffic; MV3's `declarativeNetRequest` narrows this to declared rules |
| `cookies` | Session material across sites, HttpOnly included |
| `nativeMessaging` | A bridge to a local process — code execution if the host is weak |
| `management` | Disable/remove other extensions (kill security tooling) |
| `history`, `bookmarks`, `downloads` | Browsing-profile exfiltration; downloads = payload drops |
| `clipboardRead` / `clipboardWrite` | Password-manager and copy-paste theft |
| `geolocation` | Physical tracking |
| `tabs` vs `activeTab` | `tabs` is standing access to all tab metadata; `activeTab` is a per-interaction grant — the latter is what a well-behaved extension uses |

Then grep the unpacked tree for privileged sinks and weak patterns:

```bash
grep -rn "eval(" extension_unpacked/
grep -rn "innerHTML\|document.write" extension_unpacked/
grep -rn "chrome.tabs.executeScript\|chrome.scripting.executeScript" extension_unpacked/   # MV2 / MV3 dynamic injection
grep -rn "XMLHttpRequest\|fetch(" extension_unpacked/
grep -rni "api_key\|apikey\|secret\|password\|token" extension_unpacked/                   # hardcoded secrets
grep -rn "unsafe-eval\|unsafe-inline" extension_unpacked/                                   # weak extension CSP
retire --jspath extension_unpacked/    # flag known-vulnerable bundled JS libraries
```

## Phase 2 — Dynamic testing

Load it live, instrument it, watch it talk.

```text
Chrome:  chrome://extensions -> Developer mode -> Load unpacked -> extension_unpacked/
Firefox: about:debugging -> This Firefox -> Load Temporary Add-on -> manifest.json

Inspect: popup via right-click -> Inspect; background via chrome://extensions -> "Inspect views"
Traffic: browser through a proxy (e.g. Burp) with the proxy CA trusted; watch the
         extension's own requests for API keys, plaintext HTTP, third-party telemetry
```

Message-passing probes from a normal page's DevTools console (the untrusted-origin position):

```javascript
// Direct external message — only lands if manifest declares externally_connectable
chrome.runtime.sendMessage('EXTENSION_ID', { test: 'payload' }, r => console.log(r));

// postMessage listener hunt — any content script bridging window messages
window.postMessage({ type: 'extension_message', data: 'test' }, '*');

// Prototype-pollution shaped payloads through the same paths
chrome.runtime.sendMessage('EXTENSION_ID', { '__proto__': { polluted: true } });
```

Content-script reach: DevTools → Sources → Content Scripts shows what is injected; then feed hostile DOM where the script reads it (`document.body.innerHTML = '<img src=x onerror="alert(1)">'`, or URL-fragment payloads if it parses location).

## Phase 3 — Vulnerability classes

Each class: the pattern, the test, the impact.

**XSS in extension context.** `innerHTML`/`eval` fed by page data or messages executes with the extension's own origin (`chrome-extension://EXTENSION_ID/`) — not the page's. Inject through whatever input the extension renders (page text, message payloads, its own options page) and prove script runs in the extension origin.

**Unvalidated message bridge (the core mistake).** Content script forwards `window` messages without checking `event.origin`; background trusts any `onMessage` without checking `sender`. Chain: hostile page → `postMessage` → content script → `runtime.sendMessage` → background `eval`/executeScript/fetch. The `eval(message.code)` shape is outright RCE in extension privilege; subtler shapes below.

**Extension as CORS proxy.** Background worker exposing `fetch(request.url)` over messaging gives any page a same-origin-to-everything oracle: internal `https://internal-service.example.net/` endpoints readable through the victim's extension. Test with a URL the page itself could never fetch.

**web_accessible_resources exposure.** Sensitive scripts/data exposed to `<all_urls>` get loaded by attacker pages (`<script src="chrome-extension://EXTENSION_ID/sensitive.js">`), leaking logic and tokens. MV3 requires per-match exposure — check both the exposure and the `matches`.

**Storage as a credential store.** `chrome.storage.local` holds tokens/API keys unencrypted; reachable via any XSS in extension context or by other local readers. `chrome.storage.local.get(null, ...)` dumps it during review.

**Clickjacking the extension UI.** If an extension page (popup/options) renders in an iframe without framing protection, overlay UI steals the click into a privileged action. Embed `chrome-extension://EXTENSION_ID/popup.html` in an invisible iframe over a lure.

**Native messaging escalation.** Find the host manifests (Windows `%LOCALAPPDATA%\Google\Chrome\User Data\NativeMessagingHosts\`, macOS `~/Library/Application Support/Google/Chrome/NativeMessagingHosts/`, Linux `~/.config/google-chrome/NativeMessagingHosts/`): a JSON manifest naming the binary, stdio transport, `allowed_origins` pinning extension IDs. Audit the binary like any local service: command injection in its message handling is native code execution with user rights; `allowed_origins` widened or ID-collided is the gating question.

**Extension enumeration (recon, not a bug).** Pages historically probed `chrome-extension://ID/icon.png`-style resources to fingerprint installed extensions (which security tooling the user runs — see [[edr]] for the endpoint-side picture). MV3's resource gating has been closing the oracle: treat as best-effort recon, expect false negatives.

## Phase 4 — Reporting

Developer first, then platform: Chrome via Google's app-security program (`chrome-webstore-support@google.com`, google.com/about/appsecurity — store extensions are in VRP scope); Firefox via `addons-security@mozilla.com` / Mozilla's bug bounty. Lead with the capability grade (manifest + reach), the concrete chain, and the affected version — reviewers act on demonstrated impact to users, not on excessive grants alone.

## Tool mapping (in prose)

CRXcavator and Tarnish pioneered automated extension analysis (permission risk scoring, dynamic webhook detection); hosted instances come and go, so the durable path is the manual workflow above plus retire for bundled-library CVEs, an unpacked tree, and DevTools. Burp covers the traffic side; the store listing itself is the recon artifact.
