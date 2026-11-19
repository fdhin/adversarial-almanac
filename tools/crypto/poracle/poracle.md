---
name: "poracle"
categories:
  - blackarch-crypto
version: "68.dcc00b0"
homepage: "https://github.com/iagox86/poracle"
packaged-by: [blackarch]
blackarch-builddate: "2025-04-29"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "iagox86/poracle"
github-stars: 95
github-pushed: "2019-01-17"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# poracle

> Ruby padding-oracle library with demo scripts: implement the oracle as a small Ruby block, then decrypt captured ciphertext or forge ciphertext for chosen plaintext.

## What it's for

poracle (Ron Bowes) demonstrates padding-oracle exploitation as a Ruby library rather than a ready-made CLI. You write a short Ruby program — usually starting from the bundled `Demo.rb` — that constructs a `Poracle` instance with the cipher's block size and a block receiving candidate byte strings; the block performs whatever request the target needs and returns `true` for valid padding, `false` otherwise. The library then exposes `decrypt` (recover the plaintext of captured ciphertext) and `encrypt` (produce ciphertext that decrypts to arbitrary plaintext — noticeably slower, and its first block is garbage unless you control the IV). A bundled Sinatra app, `RemoteTestServer.rb`, provides a deliberately vulnerable encrypt/decrypt endpoint to practice against. It is the teaching-grade member of the padding-oracle family (padbuster, padre, pax-oracle): the others wrap the attack in a CLI, poracle makes you write the oracle yourself. It needs Ruby and the `httparty` gem for the demo.

## Typical use cases

### Practice the attack against the bundled vulnerable server

```
ruby RemoteTestServer.rb
ruby Demo.rb
```

Outcome: the first command starts a Sinatra app on port 20222 exposing `/encrypt` and `/decrypt/<hex>` (the decrypt path reveals only success or failure); the second runs a complete decrypt-then-encrypt cycle against it, printing the recovered plaintext and the forged ciphertext — a self-contained demonstration of the whole attack.

### Decrypt a captured token against a live oracle

```ruby
require 'httparty'
require './Poracle'

poracle = Poracle.new(16) do |data|
  url = "https://target.example.com/decrypt/#{data.unpack('H*').pop}"
  HTTParty.get(url).parsed_response !~ /Fail/
end

puts poracle.decrypt(['4A5B6C7D8E9F0A1B2C3D4E5F60718293'].pack('H*'))
```

Outcome: the block is called with each candidate byte string; `decrypt` drives it until every block is recovered and returns the plaintext of the hex-encoded ciphertext. `Poracle.new(16)` matches AES; use 8 for DES-family ciphers — if unsure, try both.

### Encrypt chosen plaintext through the same oracle

```ruby
result = poracle.encrypt('user=admin')
puts result.unpack('H*').pop
```

Outcome: returns ciphertext (printed here as hex) that the oracle decrypts to `user=admin`; the first block decrypts to garbage because the IV is not yours, so prepend a throwaway block when replaying.

*No corpus evidence — derived from upstream documentation.*
