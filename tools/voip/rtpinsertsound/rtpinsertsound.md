---
name: "rtpinsertsound"
categories:
  - voip
  - vulnerability
version: "3.0-1kali4+b1"
homepage: "http://www.hackingvoip.com/sec_tools.html"
origin: "kali"
recipe: "kali-dsc"
packaged-by: [kali]
build-system: make
meta-checked: "2026-11-09"
github-repo: "foreni-packages/rtpinsertsound"
github-stars: 2
github-pushed: "2015-09-20"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rtpinsertsound

> Injects pre-recorded audio into a live G.711 RTP stream so the receiving end plays the injected audio and mutes the legitimate sender.

## What it's for

Demonstrating RTP stream hijacking on unsecured voice networks. rtpinsertsound (Mark D. Collier and Mark O'Brien, SecureLogix, with Dustin D. Trammell on v3.0; 2006–2007) watches a live RTP stream on an interface in promiscuous mode, then interlaces spoofed G.711 u-law packets whose RTP sequence numbers and timestamps are nudged just ahead of the legitimate ones (`-f` spoof factor) so the destination accepts the injected audio and rejects the real packets — the legitimate source is muted for the duration of the playback (hardcoded limit of 30 seconds). It is unidirectional, supports only G.711 u-law with a standard 12-byte RTP header, no VLAN tagging, and assumes a little-endian host; it does not need a man-in-the-middle position as long as the target stream reaches the sniffing interface (hub, span port or MITM). v3.0 adds auto-detection of RTP session endpoints via libfindrtp. To overlay audio while the original stays audible, use [[rtpmixsound]]; for pure packet flooding see [[rtpflood]].

## Typical use cases

### Replace a live call's audio with a WAV file

WAV input must be mono, uncompressed PCM, 8 kHz, 8- or 16-bit (convert other files with sox, e.g. `sox -V in.wav -r 8000 out.wav resample -ql`).

```
rtpinsertsound message.wav -i eth0 -a 198.51.100.10 -A 49170 -b 198.51.100.20 -B 49172 -f 1 -j 10
```

Outcome: the tool loads the WAV, syncs to the stream from 198.51.100.10:49170 to 198.51.100.20:49172 on eth0 and starts interlacing spoofed RTP packets; the called party hears the injected message while the legitimate audio is muted for up to 30 seconds. `-f 1` sets the header-spoof factor (default 2); `-j 10` delays each spoofed packet to ~18 ms after its trigger packet (default 80 = as soon as possible). A long pause while syncing means the 4-tuple is wrong or the call has ended.

### Auto-detect the RTP session (v3.0)

```
rtpinsertsound message.wav -i eth0
```

Outcome: v3.0's libfindrtp support discovers the RTP session endpoint addresses and ports on the interface by itself, so only the audio file is mandatory — the injection proceeds as above without `-a`/`-A`/`-b`/`-B`.

### Inject audio from a captured G.711 stream

```
rtpinsertsound g711capture -i eth0 -a 198.51.100.10 -A 49170 -b 198.51.100.20 -B 49172
```

Outcome: an argument without a `.wav` extension is treated as a tcpdump-format capture file containing only G.711 u-law RTP/UDP/IP/Ethernet packets (e.g. recorded from an Asterisk playback with Wireshark), and its payloads are replayed into the target stream instead of a WAV file's.

*No corpus evidence — derived from upstream documentation.*
