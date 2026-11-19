---
name: "soapui"
categories:
  - blackarch-proxy
version: "5.8.0"
homepage: "https://www.soapui.org/"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2025-02-17"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "SmartBear/soapui"
github-stars: 1718
github-pushed: "2026-10-28"
github-archived: false
github-issues: 147
github-match: "search"
gh-checked: "2026-11-07"
worth: 6.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 19
gh-activity-checked: "2026-11-09"
---

# soapui

> Graphical and scriptable functional testing suite for SOAP and REST web services.

## What it's for

SoapUI is SmartBear's open-source testing suite for web services: point it at a WSDL or REST definition and it lets you inspect the API, build and run functional tests, mock services, and extend behaviour with Groovy scripting; the surrounding product family adds load and security testing on the same projects. It is primarily a desktop GUI (a Java application) for exploratory API testing, while automation runs through the soapui-maven-plugin in CI pipelines such as Jenkins or through the headless runner scripts shipped in the distribution's `bin` directory. Tests are organised as projects containing test suites, test cases and test steps, with assertions on responses — a standard choice for regression-testing SOAP endpoints where a plain HTTP client is too low-level. The packaged distribution is the SmartBear binary release; the GitHub repository builds the same tool from source with Maven.

*No corpus evidence — derived from upstream documentation.*
