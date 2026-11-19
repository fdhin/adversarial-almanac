---
name: "red-team-goad-lab-proxmox"
categories:
  - backdoor
version: "git (2026-09-26)"
homepage: "https://github.com/pho5nix/red-team-goad-lab-proxmox"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "pho5nix/red-team-goad-lab-proxmox"
github-stars: 51
github-pushed: "2026-09-26"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 15
gh-activity-checked: "2026-11-09"
worth: 5.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 0
---

# red-team-goad-lab-proxmox

> Documentation repo: a seven-part build guide for a complete red-team home lab on one Proxmox host — pfSense segmentation, operator/C2/redirector VMs, and a GOAD Active Directory range with a SIEM.

## What it's for

This repository is a build guide, not software: it contains a README plus seven ordered guides ("Part 1–7") that walk through standing up a self-contained red-team lab on a single Proxmox host. The finished lab has a pfSense VM segmenting everything into VLANs — an operator segment holding a Kali VM and an Ubuntu C2 server, a separate segment for HTTP/S and DNS/SSH redirector VMs, and an isolated Windows segment running GOAD (Game of Active Directory): five Windows machines spread across three AD domains (three domain controllers plus MSSQL/IIS and MSSQL/AD-CS member servers), monitored by an ELK SIEM VM for the blue-team view. The later parts cover granting the provisioning VM controlled access to Proxmox, building the Windows templates with Packer, creating the VMs with Terraform and installing GOAD with its Ansible playbooks, then verification, snapshots and troubleshooting. The README documents a full VLAN/subnet plan and an honest resource budget — roughly 28 vCPU, 48 GB RAM and 620 GB thin-provisioned disk for the complete build, with GOAD-Light and MINILAB variants for smaller hosts — and warns that Packer/Terraform/GOAD command syntax drifts between GOAD releases, so every command must be checked against your freshly cloned GOAD repo at build time. There is no CLI of its own to document; the artifact is the walkthrough itself.

*No corpus evidence — derived from upstream documentation.*
