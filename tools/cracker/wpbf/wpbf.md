---
name: "wpbf"
categories:
  - blackarch-cracker
version: "7.11b6ac1"
homepage: "https://github.com/dejanlevaja/wpbf"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "dejanlevaja/wpbf"
github-stars: 5
github-pushed: "2014-05-26"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wpbf

> Multithreaded WordPress login brute forcer that sprays one password list at the fixed username `admin` across a whole list of sites.

*No corpus evidence — derived from upstream documentation.*

## What it's for

wpbf (Dejan Levaja; Python 2 + requests) runs a dictionary attack against
the `wp-login.php` form of any number of WordPress sites at once. It reads
a file of site base URLs and a password file, then POSTs `log=admin` plus
every password to each site's `/wp-login.php` with 50 worker threads
(defaults), detecting success by the session cookie the server sets after
a valid login. There are no command-line options: the username
(`admin`), thread count, timeout and verbosity are constants at the top of
the script and must be edited there. It has no rate-limit evasion, no
username enumeration and no XML-RPC path — it is the plain, fast,
form-login variant of a WordPress brute force.

## Typical use cases

### Brute-force `admin` across many WordPress sites

Run the password list against every site in a file.

```
wpbf sites.txt passwords.txt hits.txt
```

Outcome: after an interactive legal-use confirmation prompt (`y`), loads
the sites and passwords, then attacks all sites concurrently with 50
threads; each site URL is tried with stripped trailing `/`. For every
valid credential it prints `[*] Own3d ==> Site: <site>  Password:
<password>` and appends a `<site>::<password>` line to `hits.txt`, which
afterwards holds all recovered logins. To attack a different username,
change the `user` constant at the top of the script before running.
