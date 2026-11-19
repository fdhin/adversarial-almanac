---
name: "rtpmixsound"
categories:
  - voip
  - vulnerability
version: "3.0-1kali5+b1"
homepage: "http://www.hackingvoip.com/sec_tools.html"
origin: "kali"
recipe: "kali-debian"
packaged-by: [kali]
build-system: make
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# rtpmixsound

> Mixes pre-recorded audio into a live G.711 RTP stream in real time, so the listener hears the injected sound and the original conversation simultaneously.

## What it's for

Demonstrating RTP stream manipulation without fully displacing the original audio. rtpmixsound (Mark D. Collier and Mark O'Brien, SecureLogix, with Dustin D. Trammell on v3.0; 2006–2007) watches a live RTP stream on a promiscuous interface and interlaces spoofed G.711 u-law packets whose header values are nudged just ahead of the legitimate ones (`-f` spoof factor), mixing the pre-recorded audio with the live stream rather than muting it — that mixing, versus [[rtpinsertsound]]'s replacement behavior, is the difference between the two sibling tools. The same constraints apply: G.711 u-law only, standard 12-byte RTP header, no VLAN tagging, little-endian host, a sniffing position on the target stream (hub, span port or MITM), and v3.0 libfindrtp auto-detection of the session endpoints. For packet-count flooding of RTP processors see [[rtpflood]].

## Typical use cases

### Mix a WAV file into a live call

WAV input must be mono, uncompressed PCM, 8 kHz, 8- or 16-bit (convert other files with sox, e.g. `sox -V in.wav -r 8000 out.wav resample -ql`).

```
rtpmixsound background.wav -i eth0 -a 198.51.100.10 -A 49170 -b 198.51.100.20 -B 49172 -f 1 -j 10
```

Outcome: the tool syncs to the stream from 198.51.100.10:49170 to 198.51.100.20:49172 on eth0 and mixes the WAV's audio into it as spoofed RTP packets; the receiving party hears both the injected audio and the ongoing conversation at the same time for up to the 30-second playback limit. `-f 1` sets the header-spoof factor (default 2); `-j 10` delays each spoofed packet to ~18 ms after its trigger packet (default 80 = as soon as possible).

### Auto-detect the RTP session (v3.0)

```
rtpmixsound background.wav -i eth0
```

Outcome: v3.0's libfindrtp support sniffs out the RTP session endpoint addresses and ports on its own — only the audio file is mandatory, and the mix proceeds into the first detected session without `-a`/`-A`/`-b`/`-B`.

### Mix audio from a captured G.711 stream

```
rtpmixsound g711capture -i eth0 -a 198.51.100.10 -A 49170 -b 198.51.100.20 -B 49172
```

Outcome: an argument without a `.wav` extension is treated as a tcpdump-format capture of G.711 u-law RTP/UDP/IP/Ethernet packets, and its audio is mixed into the target stream instead of a WAV file's.

*No corpus evidence — derived from upstream documentation.*
