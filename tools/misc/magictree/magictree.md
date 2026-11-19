---
name: "magictree"
categories:
  - blackarch-misc
version: "1.3"
homepage: "http://www.gremwell.com"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# magictree

> Java GUI that consolidates pentest data into one queryable tree and generates the report from it.

## What it's for

MagicTree (Gremwell) is a penetration-tester productivity tool: it stores all engagement data — hosts, ports, services, vulnerabilities, notes — in a single tree structure and then does "the most cumbersome and boring part of penetration testing — data management and reporting" for you. It replaces grepping piles of scan output and copy-pasting into a document: results import directly from Nmap XML, Nessus, IBM Rational AppScan XML, Imperva Scuba XML and other formats; the tree can be queried (e.g. open ports on all hosts, findings grouped by severity); external commands can be executed against selected tree nodes; and the final report is generated from OpenOffice/LibreOffice or Microsoft Word templates. Version 1.3 (2013) was the last release; the upstream distributes a single Java JAR, and the packaged `magictree` command simply runs it. It is a desktop application — all real interaction happens in the GUI, so this note documents only its launcher.

## Typical use cases

### Launch the data-management workspace

Start the MagicTree GUI.

```
magictree
```

Outcome: the Swing application opens (the wrapper runs `java -jar /usr/share/magictree/magictree.jar`). Typical flow inside: import an Nmap XML via the import dialog to populate the host/port tree, query it (open TCP ports per host, findings by severity), run external tools against selected nodes, then generate a report document from a Word/LibreOffice template.

*No corpus evidence — derived from upstream documentation.*
