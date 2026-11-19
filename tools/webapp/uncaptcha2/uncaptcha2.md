---
name: "uncaptcha2"
categories:
  - blackarch-webapp
version: "7.473f33d"
homepage: "http://uncaptcha.cs.umd.edu/"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "ecthros/uncaptcha2"
github-stars: 4910
github-pushed: "2019-01-18"
github-archived: false
github-issues: 6
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# uncaptcha2

> Research proof-of-concept that solves Google reCAPTCHA v2 audio challenges with an off-the-shelf speech-to-text API at roughly 90% accuracy.

## What it's for

unCaptcha2 (Nicholas Carlini, ecthros) is the 2018 successor to the unCaptcha attack: it defeats Google's reCAPTCHA v2 by switching to the audio challenge, downloading it, transcribing it with a free speech-to-text API (Google, Microsoft or IBM), and typing the answer back. Because reCAPTCHA detects browser-automation engines like Selenium, the tool instead drives a real Firefox window by simulating mouse clicks at hardcoded screen coordinates (pyautogui). It is tuned to Google's reCAPTCHA demo site, its authors' API keys were removed before release, and the README states the repo will not be updated — treat it as a research artifact demonstrating that audio challenges were the weak link, not as a production captcha-solving service.

## Typical use cases

### Run the attack against the reCAPTCHA demo site

One flagless launch; all configuration is inside the scripts.

```
uncaptcha2
```

Outcome: opens a private Firefox window, navigates to the reCAPTCHA demo, requests the audio challenge, transcribes it via the configured speech-to-text API, submits the answer and logs each step. Before it can run, the screen coordinates at the top of /usr/share/uncaptcha2/run.py must be adapted to your desktop (`xdotool getmouselocation --shell` helps find them) and speech-to-text credentials set in queryAPI.py.

No corpus evidence — derived from upstream documentation (README of ecthros/uncaptcha2).
