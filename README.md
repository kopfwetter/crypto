# Kopfwetter Crypto

Spezifikation, Testvektoren und Referenz-Prüfung der Ende-zu-Ende-Verschlüsselung von
[Kopfwetter](https://kopfwetter.app), einem freien Stimmungstagebuch. Ziel ist, dass das Verfahren unabhängig von der
App geprüft und in anderen Tagebuch- und Gesundheits-Apps übernommen werden kann.

| Teil | Status |
| --- | --- |
| [SPEC.md](SPEC.md) – Protokoll `kopfwetter-app/v2` (Argon2id, HKDF, AES-256-GCM, HMAC-Kennungen, Auffüllen) | umgesetzt in der App |
| [vectors/](vectors/) – Testvektoren und Prüfung | umgesetzt |
| [drafts/teilen.md](drafts/teilen.md) – Teilen per verschlüsseltem Link | Entwurf |
| [drafts/pqc.md](drafts/pqc.md) – Post-Quanten-Kryptografie (hybrid, X-Wing) | Entwurf |

## Prüfen

```bash
npm ci
npm run check            # Testvektoren gegen WebCrypto und den noble-Ersatz
pip install -r requirements.txt
npm run check:generate   # vectors.json entspricht dem unabhängigen Python-Generator
```

`KOPFWETTER_APP=/pfad/zur/app npm run check` prüft zusätzlich, dass die App dieselben Kennungen verwendet.

Die Testvektoren erzeugt `vectors/generate.py` unabhängig vom JavaScript-Code mit Python (`argon2-cffi`,
`cryptography`). `vectors/check.mjs` rechnet sie mit WebCrypto und mit `src/subtle-noble.js` nach, einem Ersatz für
WebCrypto auf Basis von @noble/hashes und @noble/ciphers. Argon2id kommt in beiden Fällen aus hash-wasm.

## Sicherheit

Es gibt bisher kein externes Audit. Befunde bitte vertraulich melden, siehe [SECURITY.md](SECURITY.md).

## Lizenz

Apache-2.0, siehe [LICENSE](LICENSE). Die App selbst steht unter AGPL-3.0.
