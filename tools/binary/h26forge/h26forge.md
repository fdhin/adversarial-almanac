---
name: "h26forge"
categories:
  - binary
homepage: "https://github.com/h26forge/h26forge"
github-repo: "h26forge/h26forge"
github-match: "exact"
origin: "awesome-pentest"
recipe: "upstream"
github-stars: 337
github-pushed: "2026-07-14"
gh-checked: "2026-11-14"
worth: 5.5
worth-basis: "full (activity+popularity+recency+adoption)"
packaged-by: []
---

# h26forge

> Domain-specific infrastructure for generating and editing syntactically valid but spec-non-compliant H.264 bitstreams — purpose-built for finding memory-safety bugs in video decoders.

## What it's for

H26Forge (USENIX Security 2023, "The Most Dangerous Codec in the World") entropy-decodes an Annex B H.264 stream into its syntax elements, lets you mutate or randomize them, and re-encodes the result into a bitstream that parsers still accept — the exact recipe for stressing decoders where classic fuzzers produce unparseable garbage. It is aimed at media exploit development: its generator has produced real CVEs in FFmpeg/VLC, Firefox, and iOS CoreMedia (including 0-click kernel bugs). Despite the name it is an H.264/AVC tool first — an `--hevc` flag and an experimental mode only partially handle H.265 SPS editing. Inputs and outputs are raw Annex B bitstreams (use ffmpeg to demux MP4 first); a `--mp4`/`--mp4-frag` output path muxes results for player and Media Source Extensions testing.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Generate random non-compliant videos

```
./h26forge generate -o out.264
```

Outcome: writes a fresh Annex B H.264 file whose syntax elements are randomly filled per the default config — the starting corpus for decoder fuzzing; `./scripts/gen_100_videos.sh` batches 100 at a time.

### Generate with a custom randomness range

```
./h26forge generate -o out.264 -c config/default.json
```

Outcome: generation constrained to the syntax-element ranges you edited in the JSON config (copy `config/default.json` and narrow fields to target one structure, e.g. SPS parsing only).

### Edit syntax elements with a Python transform

```
./h26forge modify -i input.264 -o output.264 -t transforms/slice_all_pcm.py --arg 0
```

Outcome: decodes `input.264` to a JSON syntax-element tree, runs the `modify_video(ds)` function in your transform script over it, and re-encodes to `output.264` — the surgical path for building PoCs (the repo ships the CVE-2022-22675 PoC transform as a worked example).

### Randomize slices of an existing video

```
./h26forge randomize -i input.264 -o out.264 --randomize-all-slices
```

Outcome: keeps your video's structure but randomizes slice data (headers too with `--randomize-slice-header`) — useful for stressing hardware decoders and for finding stateful bugs reached only late in a stream.

### Round-trip through JSON

```
./h26forge --json generate -o out.264
./h26forge synthesize -i out.264.json -o synth.264
```

Outcome: the first command emits `out.264.json` describing every recovered syntax element; after editing that JSON with any tool, `synthesize` re-encodes it into a valid bitstream.

### Mux a bitstream into a (fragmented) MP4

```
./h26forge mux -i in.264 -o out.mp4 --mp4-frag
```

Outcome: wraps the raw bitstream in an MP4 container without re-encoding — `--mp4-frag` produces the fragmented files browsers consume via Media Source Extensions.
