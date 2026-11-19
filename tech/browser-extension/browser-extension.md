---
technology: "Browser extensions (Chrome / Firefox add-on security surface)"
kind: overview
audience: offensive-security
status: upstream-only
family: web-platforms
layer: privileged client-side code inside the browser — manifest-granted capabilities, content-script page context, background service worker, optional native-messaging bridge to a local process
language: "JavaScript / TypeScript"
provenance: "upstream-only (pentest-book + primary sources); zero corpus evidence by design — owner-approved new-unit wave (PB-INGEST) 2026-11-13; Manifest V3 semantics verified against Google's primary developer documentation during the wave (MV3 generally available from Chrome 88; background/event pages replaced by service workers; blocking webRequest listeners replaced by the declarativeNetRequest API; remotely-hosted code and execution of arbitrary strings removed). Carries zero CVEs by design — the unit documents a capability and trust model, not version-gated bugs"
source: "six2dez/pentest-book (others/browser-extension-security.md, no license — rewritten with attribution), accessed 2026-11-13"
---

# Browser extensions — the trusted code your browser runs for you

> A browser extension is a capability bundle wearing an icon: everything it may ever do is declared in one JSON file up front, consented to in a single install dialog, and then never checked again — the operator's whole job is reading that manifest as an authorization database and asking who else can reach the privileged side.

## What it is

An extension is a small ZIP of web code plus a `manifest.json` that IS the security model. There is no sandbox story beyond what the manifest asks for: the `permissions` array enumerates API capabilities (cookies, history, tabs, downloads, clipboard, native messaging), the content-script `matches` patterns enumerate which pages the extension injects into, and the browser grants all of it at install time. The install warning is the user's one consent moment; after that, the code runs with those powers silently across every page and, with `<all_urls>`, across the victim's entire browsing life.

The platform is mid-transition and the transition changed the attack surface. Manifest V2 (the legacy format) allowed persistent background pages, blocking network interception (`webRequestBlocking`), and remotely-hosted code. Manifest V3 — generally available from Chrome 88 and now required for store listings — replaces the background page with a non-persistent service worker, replaces blocking request handling with the declarative `declarativeNetRequest` API, moves host access into a separate `host_permissions` field, and bans remotely-hosted code and arbitrary-string execution (`eval` of fetched payloads is gone in principle). Enterprise policy channels can keep legacy behavior alive for force-installed extensions, so both models are live in the wild: read the `manifest_version` field first — it tells you which decade of abuse techniques apply.

Firefox keeps a parallel model (`.xpi` archives, `about:debugging` for temporary installs) with broadly similar manifest semantics and its own store review pipeline. The operator takeaway is unchanged across both: **review is not audit**. Store review is automated plus sampled human review of a permission diff; a permission set that is legal but excessive (`<all_urls>` + `cookies` + `nativeMessaging` in a currency converter) passes review and is still a full browsing-session implant. Repeated real-world waves of bought-out or cloned extensions turning into credential harvesters are the trust gap in production form.

## The anatomy that matters on an engagement

```
manifest.json ── declares ──> permissions / host_permissions
     │                              │
     ▼                              ▼
content scripts ──(isolated JS world, SHARED DOM)──> web page (untrusted input frontier)
     │   chrome.runtime.sendMessage / postMessage bridge
     ▼
background service worker ──(holds the API privileges: cross-origin fetch, cookies, ...)
     │   optional: externally_connectable (which SITES may message it directly)
     ▼
native messaging host ──(stdio)──> local OS process (code execution with user rights)
```

Three structural properties drive most findings:

1. **The manifest is a capability model, so privilege escalation is a manifest question.** A content script can be perfectly written and still be a full-compromise component if the background worker it messages will `eval`/inject on demand, fetch arbitrary URLs, or hand over `cookies` API results. Grade every extension by its *granted* powers, not by what its UI claims to do.
2. **The message boundary is the trust boundary.** Content scripts live in the page's DOM (untrusted input everywhere) but talk to the privileged background context. Any listener that fails to validate `sender`/`event.origin` converts a hostile web page into a driver of privileged code — the extension-as-CORS-proxy and message-to-`eval` patterns below are this one mistake.
3. **The extension ID is a stable identity.** IDs derive from the extension's key, so resources under `chrome-extension://EXTENSION_ID/` fingerprint both installed users and specific extensions — the basis of extension enumeration (tightening in MV3, see the methodology).

## Where you'll meet it

- **Enterprise estates** — force-installed extensions via policy (Chrome `ExtensionInstallForcelist`, Firefox managed extensions) are pinned by ID and shipped to every workstation: one vulnerable helper with `<all_urls>` is an org-wide browsing implant, and the registry key is your inventory list.
- **Bug-bounty targets** — password managers, SSO/passwordless helpers, request rewriters, devtools panels: extensions that by design read every page and hold session material. Their store listing is the recon report.
- **Incident response / hunting** — "is this store extension safe?" is a recurring ask; the answer is the manifest risk grade plus a static pass, not the review badge.
- **Post-exploitation** — a malicious unpacked extension is quiet persistence: it lives in the browser profile, wakes on every page, and mostly flies under endpoint telemetry built around process trees (see [[edr]] for what that telemetry does and does not see).

## Reading the framework's mind

- The install dialog is generated from the manifest permissions — a *bigger* permission set produces a *scarier* dialog, so polished extensions ask broadly once and never again. Treat generous manifests as intent, not accident.
- MV3's service worker sleeps; listeners must be registered synchronously at worker start or messages are dropped. Waking/sleeping behavior is why MV3 extensions often re-fetch state — a traffic tell when auditing one live.
- `externally_connectable` is opt-in: absent it, only the extension's own pages/scripts can use `runtime.sendMessage`; present it, the listed third-party origins can drive the background directly. Its absence/presence reshapes the whole test plan.
- `web_accessible_resources` decides what web content can load from the extension — historically the installed-extension enumeration oracle. MV3 requires per-`matches` exposure and the browsers have been closing the oracle; probe, don't assume either way.
- Native messaging hosts register in OS-specific manifests with `allowed_origins` pinning which extension IDs may spawn them — a local binary gated only by a JSON file that an installer (or you, with the right rights) can edit.

## Where to go next

The full phased method — acquisition, static grading, dynamic testing, per-vulnerability patterns, and reporting routes — is in [[browser-extension-bug-bounty]].
