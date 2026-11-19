---
tool: "graphicator"
build-system: "python"
upstream: "https://github.com/cybervelia/graphicator"
recipe: "upstream"
license: "MIT"
---

# Installing graphicator

> GraphQL enumeration and extraction tool — introspection-driven query generation and response dumping.

Source: howtohunt feed; upstream [`cybervelia/graphicator`](https://github.com/cybervelia/graphicator).

## From source

```bash
git clone https://github.com/cybervelia/graphicator.git
cd graphicator
python3 -m pip install -r requirements.txt
python3 graphicator.py --target https://target.example.com/graphql
```

## Docker

```bash
docker run --rm -it -p8005:80 cybervelia/graphicator --target https://target.example.com/graphql --verbose
```

On completion the results zip is served over HTTP on the mapped port (8005
here); the data is deleted when the container stops.

## Not packaged

No distro package ships graphicator (checked 2026-11-13).

Backlinks: [[webapp]] · [[graphicator]]
