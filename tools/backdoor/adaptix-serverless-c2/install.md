---
tool: "adaptix-serverless-c2"
build-system: "make"
upstream: "https://github.com/stillbigjosh/adaptix-serverless-c2"
recipe: "upstream"
version: "git (2026-10-29)"
license: "see repository"
---

# Installing adaptix-serverless-c2

> Serverless C2 transport plugin for AdaptixC2 v1.2 using AWS Lambda + DynamoDB as the relay infrastructure.

Source: kitploit directory; upstream [`stillbigjosh/adaptix-serverless-c2`](https://github.com/stillbigjosh/adaptix-serverless-c2).

## Prebuilt / releases

Check [upstream releases](https://github.com/stillbigjosh/adaptix-serverless-c2/releases) for binaries.

## Any other Linux distro

```bash
git clone https://github.com/stillbigjosh/adaptix-serverless-c2.git
cd adaptix-serverless-c2
make
sudo make install    # if no install target, copy binary to /usr/local/bin
```

## Not yet packaged

No distro packages this tool yet (checked 2026-11-07).

Backlinks: [[backdoor]] · [[adaptix-serverless-c2]]
