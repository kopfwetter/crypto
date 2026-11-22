# Entwurf: Post-Quanten-Kryptografie

> **Status: Entwurf, nicht umgesetzt.**

## Einordnung

| Baustein | Verfahren heute | Quantenrisiko | Plan |
| --- | --- | --- | --- |
| Inhalte | AES-256-GCM | gering | bleibt |
| Passwort | Argon2id, HKDF-SHA-256 | gering | bleibt |
| Teilen per Link | symmetrisch (`teilen.md`) | gering | bleibt symmetrisch |
| Dauerhafte Freigabe an eine Praxis | – | hoch bei klassischem Schlüsselaustausch | hybrid |
| Neues Gerät koppeln | – | hoch bei klassischem Schlüsselaustausch | hybrid |
| Transport | TLS über Browser/Cloudflare | Browser verhandeln X25519MLKEM768 | Konfiguration prüfen |

Risiko „harvest now, decrypt later“: Mitgeschnittene Schlüsselaustausche mit klassischen Public-Key-Verfahren könnten
später mit Quantencomputern gebrochen werden. Symmetrische Verfahren mit 256-Bit-Schlüsseln sind davon kaum betroffen.

## Plan

- **Hybrides KEM:** X-Wing (X25519 + ML-KEM-768), draft-connolly-cfrg-xwing-kem. Ausweichweg: hybride KEMs aus
  draft-ietf-hpke-pq. Hybrid entspricht der Empfehlung des BSI (TR-02102-1).
- **Bibliothek:** @noble/post-quantum (MIT), dieselbe Familie wie @noble/hashes und @noble/ciphers.
  WebCrypto-ML-KEM, wo der Browser es anbietet.
- **Versionierung:** Jede Freigabe und jeder Schlüssel trägt eine Kennung des Verfahrens
  (z. B. `kopfwetter-app/share-kem/xwing-v1`), damit Algorithmen später getauscht werden können.
- **Testvektoren:** wie für Protokoll v2, unabhängig erzeugt und gegen eine zweite Implementierung geprüft.
