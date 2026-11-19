---
name: "autorize"
categories:
  - webapp
homepage: "https://github.com/portswigger/autorize"
origin: "pentest-book"
recipe: "upstream"
packaged-by: []
build-system: python
github-repo: "portswigger/autorize"
github-stars: 305
github-match: "exact"
gh-checked: "2026-11-13"
worth: 6.9
worth-basis: "full (activity+popularity+recency+adoption)"
license: custom:unknown
---

# autorize

> Burp Suite extension that automatically replays your traffic as a low-privileged or unauthenticated user to catch broken access control.

## What it's for

Autorize (Jython extension by Barak Tawily) is an automatic authorization-enforcement detection tool for Burp Suite: you browse the application as a high-privileged user, and for every request the proxy sees, Autorize transparently replays it with a low-privileged user's credentials (a cookie or `Authorization` header you configure) and optionally with no credentials at all, then compares the three responses and flags whether access control was actually enforced. This is the mechanical answer to hunting IDORs and broken object/endpoint authorization at scale — classes authenticated crawlers and scanners traditionally miss — and it also exposes endpoints with no authentication at all. Verdicts appear per request in a table as red `Bypassed!` (the low-privileged copy got the content), green `Enforced!` (it was denied like it should be), or yellow `Is enforced???` (undetermined — needs a fingerprint). Detection is heuristic: it compares response content, headers, length and full-response equality, and when it can't decide you teach it with enforcement detectors (literal strings or regex on the body/headers, e.g. `You are not authorized to perform this action`), so expect some tuning on first contact with an app. It integrates with proxy traffic and Repeater, supports multiple simultaneous low-privileged users for side-by-side role comparison, and filters what it tests via Burp scope, whitelists, blacklists and regex.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Test an authenticated app for authorization bypasses

The core workflow: browse as admin, watch Autorize judge every request.

```
Burp: Autorize tab -> Configuration
  -> set the low-privileged user's Cookie / Authorization header (replaced if present, added if missing)
  -> optionally enable "Check unauthenticated"
  -> click "Intercept is off" to start interception
Browser: proxy through Burp, browse the app as the high-privileged user
```

Outcome: every request you make is replayed with the low-privileged (and optionally unauthenticated) context, and the Autorize table lists each endpoint with its verdict — red `Bypassed!` rows are the findings; select any row to inspect the original, modified and unauthenticated request/response pairs.

### Teach Autorize the app's "denied" response

Fix the yellow `Is enforced???` verdicts with fingerprints.

```
Autorize -> Enforcement Detection: add a literal string or regex
  e.g. "You are not authorized to perform this action"
  (separate detectors for low-privileged and unauthenticated checks;
   can match response body content, headers, or content length)
```

Outcome: responses containing the fingerprint are classified as properly enforced going forward, clearing the false-yellow noise; regex detectors handle whole classes of denial messages rather than exact strings.

### Cut noise with interception filters

Only test what matters.

```
Autorize -> Interception Filters:
  Burp scope / whitelist / blacklist / regex on URLs
  (defaults already exclude static assets like images and scripts)
```

Outcome: requests matching the filters are skipped entirely, keeping the table focused on in-scope functionality and improving performance during large browsing sessions.

### Compare multiple low-privileged roles at once

Run several users side by side.

```
Autorize -> add multiple low-privileged users (each with its own cookie/header)
Autorize table: click a user's column ("User 1 Authz. Status" etc.) to expand their viewer
```

Outcome: each configured user gets its own replay of every request plus a dedicated request/response viewer, so you can view two roles' responses side by side and quickly spot where one role gets content the other is denied — the eye-icon toggle in the Request/Response Viewers tab controls which viewers are shown.
