---
name: "ysoserial.net"
categories:
  - webapp
homepage: "https://github.com/pwntester/ysoserial.net"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: dotnet
github-repo: "pwntester/ysoserial.net"
github-stars: 3805
github-match: "exact"
gh-checked: "2026-11-13"
worth: 5.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
license: "MIT"
---

# ysoserial.net

> A .NET gadget-chain payload generator: wraps a command in a formatter-specific gadget chain (BinaryFormatter, Json.NET, ViewState, ...) for exploiting unsafe deserialization in .NET applications.

## What it's for

Despite the name, this is not the same project as [[ysoserial]] — that note covers Chris Frohoff's original Java tool, while ysoserial.net is Alvaro Muñoz (pwntester)'s independent .NET counterpart, explicitly inspired by the Java project. You pick a gadget chain discovered in common .NET libraries (ActivitySurrogateSelector, ClaimsPrincipal, DataSet, ObjectDataProvider, WindowsIdentity, TextFormattingRunProperties and many more), a formatter the target application uses to deserialize (BinaryFormatter, LosFormatter/ObjectStateFormatter, NetDataContractSerializer, SoapFormatter, Json.NET, XmlSerializer, DataContractSerializer, ...), and a command; the tool emits the serialized blob to stdout in raw, base64, URL-encoded or hex form. When an application with the required gadgets available unsafely deserializes the blob, the chain fires and the command runs on the application host — the vulnerability being the application's unsafe deserialization, not the libraries themselves. Beyond raw payload generation, a plugin architecture produces attack-ready payloads for specific CVEs: DotNetNuke (CVE-2017-9822), SharePoint (CVE-2020-1147, CVE-2019-0604, CVE-2018-8421), signed ASP.NET ViewState forgery from leaked machineKey values, and TransactionManager.Reenlist. It is a Windows/.NET tool (runs under mono on Linux); the v2 branch is a .NET Framework 2.0-compatible fork maintained by irsdl (see [[ysonet]] for that author's own generator).

No corpus evidence — derived from upstream documentation.

## Typical use cases

### List every gadget chain with its supported formatters

Run with no arguments before choosing a payload.

```
ysoserial.exe --fullhelp
```

Outcome: prints the gadget catalog — each chain (ActivitySurrogateDisableTypeCheck through WindowsIdentity) with the formatters it supports, whether it is bridge-capable, its extra options such as `--variant`, followed by the general options (`-g`, `-f`, `-c`, `-o`, `--minify`, `--bgc`) and the plugin list with their options; match a chain whose formatter the target actually uses.

### Find gadgets that work for a given formatter

Reverse lookup when you know the sink's formatter but not the gadget.

```
ysoserial.exe --searchformatter Json.Net
```

Outcome: lists the relevant gadgets and their formatters for the search term, so you can pick a chain compatible with the deserializer you have identified in the target application.

### Generate a command-execution payload as base64

The core invocation: gadget + formatter + command + output encoding.

```
ysoserial.exe -f BinaryFormatter -g PSObject -o base64 -c "calc"
```

Outcome: writes the BinaryFormatter-serialized PSObject payload to stdout as a single base64 line, ready to paste into any field the target base64-decodes before deserializing; add `-t` to also execute the payload locally as a self-test (it will pop calc on your own machine), and switch `-o` to `raw`, `raw-urlencode`, `base64-urlencode` or `hex` to match the transport.

### Generate a Json.NET payload in raw form

Target applications that deserialize JSON with type information.

```
ysoserial.exe -f Json.Net -g ObjectDataProvider -o raw -c "calc"
```

Outcome: prints the JSON payload object directly — an ObjectDataProvider `$type`-tagged dictionary whose `MethodName`/`ObjectInstance` invoke `Process.Start` — which you embed in the request body or field the application feeds to Json.NET's deserializer.

### Pass a complex command over stdin

Avoid escaping semicolons, pipes and ampersands in long commands.

```
cat my_long_cmd.txt | ysoserial.exe -o raw -g WindowsIdentity -f Json.Net -s
```

Outcome: reads the command from standard input instead of the command line (`-s`), so special characters in the command file never need shell escaping; the serialized payload is written to stdout as with `-c`.

### Read a file from a DotNetNuke target (CVE-2017-9822)

Use the bundled plugin instead of a generic chain.

```
ysoserial.exe -p DotNetNuke -m read_file -f win.ini
```

Outcome: emits the DNN profile XML payload (`<profile><item ...ExpandedWrapper...>`) whose WriteFile call dumps the requested file when deserialized by the vulnerable DotNetNuke instance — change the `-f` value to any path you want read back in the response.

### Run a command on a DotNetNuke target

Same plugin, execution mode.

```
ysoserial.exe -p DotNetNuke -m run_command -c calc.exe
```

Outcome: emits the profile XML wrapping a command-execution gadget; delivered to the vulnerable endpoint it executes the given command on the DNN host (`-m write_file -u <url> -f <path>` is the third mode, downloading a file from a URL and writing it to disk).

### Forge a signed ASP.NET ViewState from leaked machineKey values

Turn a known validationKey/decryptionKey into a deserialization payload.

```
ysoserial.exe -p ViewState -g ActivitySurrogateSelector --path="/default.aspx" --apppath="/" --decryptionalg=AES --decryptionkey=64-hex-key --validationalg=HMACSHA256 --validationkey=hex-key
```

Outcome: produces a signed `__VIEWSTATE` value built with the supplied machineKey parameters (algorithms and key lengths taken from the target's web.config); pasting it into the page's hidden field makes the server deserialize the embedded gadget — add `--islegacy` for .NET 4.0 and below, `--minify` to shrink the payload, and `--examples` to have the plugin print ready-made example invocations.

### Shrink a payload with gadget chaining and minification

Fit a payload through size-limited fields, e.g. the Exchange CVE-2021-42321 shape.

```
ysoserial.exe -g ClaimsPrincipal -f BinaryFormatter -c foobar -bgc ActivitySurrogateDisableTypeCheck --minify --ust
```

Outcome: nests the ActivitySurrogateDisableTypeCheck gadget (which disables .NET 4.8+ type protections) inside the ClaimsPrincipal chain via `--bgc`, then minifies the output and strips assembly-version details (`--ust`), yielding a much shorter base64 payload that also works on hardened .NET runtimes.
