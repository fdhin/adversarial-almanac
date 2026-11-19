---
tool: "ghostwriter"
build-system: "python"
upstream: "https://github.com/GhostManager/Ghostwriter"
recipe: "upstream"
license: "BSD-3-Clause"
---

# Installing ghostwriter

> Ghostwriter — open-source platform for offensive security operations: report writing, asset tracking, assessment management.

Source: pentest-book feed; upstream [`GhostManager/Ghostwriter`](https://github.com/GhostManager/Ghostwriter). Full documentation lives in the [Ghostwriter Wiki](https://ghostwriter.wiki/); this page follows its [Installation](https://github.com/GhostManager/Ghostwriter/wiki/Installation) guide.

## Deploy with Docker Compose (standard path)

Ghostwriter is a Django + PostgreSQL stack intended to run under Docker Compose. Install Docker first, then:

```bash
git clone https://github.com/GhostManager/Ghostwriter.git
cd Ghostwriter

# create the environment files from the templates
mkdir .envs && cp .envs_template/.* .envs
# edit .envs/<env>/.django: VirusTotal/Slack keys, company info, etc.

# local/development deployment:
docker-compose -f local.yml up -d
# production deployment:
docker-compose -f production.yml up -d
```

Outcome: builds and starts the containers. The `.envs` directory holds `.django` and `.postgres` settings for the `local` and `production` configurations — the `.django` file is where API keys (e.g. `VIRUSTOTAL_API_KEY`), Slack notifications and company/reporting values are set.

## First-run: seed data and create the admin

```
docker-compose -f local.yml run --rm django /seed_data
docker-compose -f local.yml run --rm django python manage.py createsuperuser
```

Outcome: the first command loads the required Django fixtures (domain statuses, project types, finding severities — Ghostwriter is largely unusable without them); the second creates the superuser for the Django admin panel at `http://SERVER_IP:8000/admin` (the `production.yml` deployment listens on `0.0.0.0:80`).

## Update an existing deployment

```
docker-compose -f local.yml stop
docker-compose -f local.yml rm -f
docker-compose -f local.yml build
docker-compose -f local.yml up -d
```

Outcome: stops and removes the old containers, rebuilds with the new code, and brings the stack back up (substitute `production.yml` for a production deployment). New users sign up at `/accounts/signup` or are created in the admin panel.

## Not yet packaged

No distro packages Ghostwriter (checked 2026-11-13) — it is deployed from source via Docker Compose as above. Note the Arch Linux `ghostwriter` package is an unrelated Markdown editor of the same name.

Backlinks: [[automation]] · [[ghostwriter]]
