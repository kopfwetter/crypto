# Entwurf: Teilen per verschlüsseltem Link

> **Status: Entwurf, nicht umgesetzt.** Geplant für das Add-on „Teilen“ (siehe Förderantrag). Formate und Parameter
> können sich bis zur Umsetzung ändern; erst die Version mit Testvektoren gilt.

## Ziel

Eine Patientin gibt einer Psychiaterin oder einem Psychologen einen Ausschnitt ihres Tagebuchs zum Lesen frei.
Die Empfängerin braucht kein Konto. Der Server speichert nur Chiffrat und kann es nicht lesen. Wer nur den Link
abfängt, kann den Inhalt nicht entschlüsseln.

## Ablauf (Skizze)

1. Der Client stellt den Ausschnitt zusammen (Zeitraum, Inhalte; Einträge mit „nur für mich“ sind ausgeschlossen)
   und verschlüsselt ihn als eigenes **Freigabe-Paket**. Der DEK der Patientin verlässt das Gerät nie.
2. Es entstehen zwei Geheimnisse:
   - `L` – 32 Zufallsbytes, Base64url im Fragment des Links (`…/s/<id>#<L>`), erreicht nie den Server
   - `C` – ein kurzer Code (z. B. 10 Zeichen aus dem Alphabet des Wiederherstellungsschlüssels, 50 Bit), den die
     Patientin auf anderem Weg weitergibt
3. Schlüssel des Pakets: `K = Argon2id(C, salt = L, m = 64 MiB, t = 3, p = 1)` → HKDF → AES-256-GCM.
   Das teure Argon2id macht das Durchprobieren des Codes auch mit Link unpraktikabel.
4. Kennung beim Server: `id = hex(HKDF(L, "kopfwetter-app/share-id/v1")[0:16])`. Der Server kennt `id`,
   Ablaufdatum, erlaubte Öffnungen und das Chiffrat, aber weder `L` noch `C`.
5. Ablauf, Öffnungsgrenze und Widerruf setzt der Server durch; danach löscht er das Paket.
   Öffnungen werden mit Zeitpunkt protokolliert, damit die Patientin sie sieht.

## Grenzen

- Was einmal gelesen wurde, lässt sich nicht zurückholen. Die App erklärt das vor dem Teilen.
- Wer Link **und** Code hat, kann lesen, solange der Link gilt.
- Der Ansatz ist rein symmetrisch und damit nicht auf klassische Public-Key-Verfahren angewiesen (siehe `pqc.md`).

## Offene Fragen

- Länge und Alphabet des Codes; Argon2id-Parameter auf schwachen Geräten der Empfängerinnen
- Ob der Server vor dem Ausliefern einen Nachweis über `C` verlangen soll, ohne `C` zu erfahren
- Format des Pakets (JSON-Struktur, Auffüllen auf Größenstufen wie bei Datensätzen)
