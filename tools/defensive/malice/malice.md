---
name: "malice"
categories:
  - blackarch-defensive
version: "0.3.28"
homepage: "https://github.com/maliceio/malice"
packaged-by: [blackarch]
blackarch-builddate: "2020-04-30"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "maliceio/malice"
github-stars: 1863
github-pushed: "2023-04-03"
github-archived: true
github-issues: 26
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# malice

> Self-hosted, VirusTotal-style multi-engine file scanning built from Docker plugin containers.

## What it's for

Malice is an open-source malware analysis framework that aims to be a free, self-hosted VirusTotal usable at any scale. Each analyzer (ClamAV, YARA, EXIF and many more) is a Docker container plugin; the CLI runs a pipeline of plugins against a sample and merges their verdicts. Beyond single scans it can watch a folder for incoming samples, look up hashes online, manage the plugin set, and start an ELK stack (Elasticsearch + Kibana) that stores every scan result for search and dashboards. First runs download the default plugin images, which can take a while; plan for roughly 16 GB of disk and 4 GB of RAM. The upstream project is archived and releases stop at the 0.3.x series, so bundled plugin images age over time. It analyzes files you feed it — it is not an on-host antivirus.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Scan a suspicious file through all plugins

```
malice scan suspicious.bin
```

Outcome: pulls the default plugin containers on first use, runs them against suspicious.bin, and prints the merged multi-engine verdict as a markdown table that can be piped straight into a results file.

### Monitor a drop folder for new samples

```
malice watch inbox/
```

Outcome: watches inbox/ and automatically runs the full plugin pipeline against every file that appears in it — a hands-off triage queue for a malware inbox directory.

### Look up a hash before deciding to pull a sample

```
malice lookup 44d88612fea8a8f36de82e1278abb02f
```

Outcome: queries online sources for the hash (shown here: the EICAR test file's MD5) and prints any existing multi-engine verdict, letting known samples be triaged without downloading or re-scanning them.

### Browse scan history in Kibana

```
malice elk
```

Outcome: starts the Elasticsearch and Kibana containers; once up, the Kibana UI at http://localhost shows accumulated scan results — create the `malice` index pattern to explore them.

### Refresh the plugin images

```
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock malice/engine plugin update --all
```

Outcome: pulls the latest version of every installed plugin image for the Docker-in-Docker engine setup; `malice plugin` from the CLI lists, installs or removes individual plugins.
