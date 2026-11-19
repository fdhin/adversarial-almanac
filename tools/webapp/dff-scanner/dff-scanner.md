---
name: "dff-scanner"
categories:
  - blackarch-webapp
version: "1.1"
homepage: "https://www.security-net.biz/dff-default-files-and-folders-scanner-p77.html"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-14"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# dff-scanner

> PHP class library for finding predictable default files and folders on a web server.

## What it's for

DFF (Default File & Folder) scanner, written in 2007 by Ivan Markovic, probes web locations for common folder and file names — the default and predictable content that ships with CMSes, frameworks and admin panels. It is not a standalone command-line tool: the package ships two PHP classes (installed under /usr/share/dff-scanner/) that you drive from a short script — instantiate the class, set the target URL, name sources (letters, custom names, dictionary file), cURL options and scan depth, then call scan_it(). Requires PHP with cURL.

## Typical use cases

### Probe for default folders and files

```php
<?php
require_once '/usr/share/dff-scanner/dff.files.class.php';

$dff = new dffFiles();
$dff->url = 'https://target.example.com/';
$dff->custom_names = array('admin', 'blog', 'forum');
$dff->file_custom_names = array('admin', 'backup', 'dump');
$dff->file_extensions = array('.bak', '.sql', '.txt');
$dff->scan_it();
```

Outcome: requests target/<name> for each folder name and target/<file><ext> for each file name, suppresses responses matching the discovered 404 signature, and prints a report of the paths that exist; run the driver with `php driver.php` or through a browser.

### Dictionary-driven scan with recursion

```php
<?php
require_once '/usr/share/dff-scanner/dff.files.class.php';

$dff = new dffFiles();
$dff->url = 'https://target.example.com/';
$dff->use_dic_file = 'MERGE_CUSTOM';
$dff->dic_file = 'dic.txt';
$dff->custom_names = array('admin');
$dff->in_deep = 1;
$dff->scan_it();
```

Outcome: merges dic.txt with the custom names into the candidate list and scans one directory level deep inside each folder found (in_deep), scaling the sweep to larger sites.

_No corpus evidence — derived from the packaged upstream release (readme and class headers shipped inside the package)._
