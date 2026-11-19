---
name: "tlspretense"
categories:
  - blackarch-crypto
version: "v0.6.2.r22.g0a5faf4"
homepage: "https://github.com/iSECPartners/tlspretense"
packaged-by: [blackarch]
blackarch-builddate: "2021-04-14"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "iSECPartners/tlspretense"
github-stars: 93
github-pushed: "2021-09-14"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# tlspretense

> Framework that tests whether an SSL/TLS client validates certificates correctly — it intercepts the client's traffic with firewall rules and presents deliberately flawed certificate chains.

## What it's for

Most TLS testing tools examine servers; tlspretense targets the other end: the client's certificate-validation logic. It generates a battery of certificates with specific flaws (unknown CA, wrong hostname, and similar), redirects the client's outbound TLS traffic through the host's firewall (netfilter on Linux, ipfw or PF on BSD/macOS), and presents each flawed chain in turn — a test passes when the client correctly refuses to complete the handshake. You run it on a Unix host acting as the client's gateway, with the client configured to trust tlspretense's test CA so its "good" certificates are accepted. It is an iSEC Partners research framework from 2012–2013 built for Ruby 1.9-era systems, so expect dated dependencies rather than turnkey polish; only plain TLS connections are intercepted — protocols that negotiate TLS via STARTTLS (SMTP, IMAP) are explicitly unsupported.

## Typical use cases

### Scaffold a new test project

Create the working directory and configuration for a test run.

```
tlspretense init myproject
```

Outcome: creates the myproject directory containing a config.yml (test hostname, firewall/interception method, network device) plus supporting structure; edit config.yml to match your network setup before generating certificates and running tests.

### Generate the test CA and the flawed certificates

Produce the trust anchor and the certificate battery the tests will present.

```
tlspretense ca
tlspretense certs
```

Outcome: `ca` creates a fresh test CA (only needed if you do not want the default or your own), and `certs` generates the full set of test certificates — trusted chains plus flawed variants such as unknown-CA and wrong-hostname — that the runner will present to the client; the client under test must trust this CA for the "good" cases to succeed.

### Run the full client-validation suite

Execute every configured test case against the client.

```
sudo tlspretense run
```

Outcome: activates the firewall redirection, intercepts the client's TLS connections to the configured test hostname, and cycles through all test cases, starting each next test as soon as the previous finishes; every case is judged pass/fail by whether the client completed the handshake, and root is required for the firewall rules.

### Re-run only specific tests

Repeat individual named cases without the whole battery.

```
sudo tlspretense run unknownca wrongcname
```

Outcome: executes just the named test cases (here: a certificate from an untrusted CA, then one with a mismatched hostname) in the order given — useful to re-check a single reported failure or demonstrate one specific validation weakness.

*No corpus evidence — derived from upstream documentation.*
