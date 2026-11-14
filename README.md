# HealthLog

Offline-Android-App: den Tag für Daniels Health OS mit einem Tap erfassen
(Mahlzeiten mit Protein, Ja/Nein-Punkte, Gewicht, Stress, Me-Time,
Arbeitsende, Training) und als Text an den Health-OS-Chat geben. Dort
übernimmt der Skill `health-os-debrief` die Werte in den Health OS Analyzer.

Die App hat kein Netzwerk und keinen Zugriff auf den Analyzer. Der Weg ist
immer: in der App tippen → "Für Health-OS kopieren" → im Health-OS-Chat
einfügen.

APK: [dist/healthlog-1.0.apk](dist/healthlog-1.0.apk)

## Felder

Die Feldnamen sind die des Tagesdokuments im Analyzer (Collection `tage`).

| Bereich | Felder | Pflicht für "Alles erfasst" |
|---|---|---|
| Protein | `mahlzeiten` [{zeit, was, protein_g}], daraus `protein_g`, `fastenende`, `letzte_mahlzeit` | mindestens eine Mahlzeit |
| Ja/Nein | `flohsamen`, `supps_ok`, `meditation_ok`, `reveri_ok`, `atmung_ok`, `lesen_ok`, `alkohol`, `abends_gearbeitet` | ja |
| Ja/Nein optional | `handy_draussen`, `experiment_ok` | nein |
| Werte | `gewicht`, `stress_n`, `me_time_min` | ja |
| Werte optional | `energie_n`, `ellbogen_n` | nein |
| Zeiten | `arbeitsende` | Mo–Fr |
| Zeiten optional | `koffein_zuletzt` | nein |
| Training | `training_typ` (Tag A, Tag B, 4x4, Padel, Ruhetag); bei Padel `padel_min`, `padel_format` | ja |
| Text | `notiz` | nein |

Ja/Nein-Punkte haben drei Zustände: offen (nicht im Export), ja, nein.
`lesen_ok` bedeutet "am Vorabend gelesen". Die Pflichtliste entspricht dem
Abschnitt "Lücken bestimmen" im Skill; Oura-Werte und Top-Sätze kennt die
App nicht.

## Exportformat

```
HEALTHLOG 2026-11-13 (Fr)
Protein: 123 g (Untergrenze 135)
Mahlzeiten: 12:15 Skyr-Bowl 83 g; 19:00 Döner 40 g
Ja: Flohsamen, Supps, Gestern Abend gelesen
Nein: Meditation, Reveri, Atmung, Alkohol, Abends gearbeitet
Gewicht 85.9 kg | Stress 3 | Me-Time 120 min
Arbeitsende 18:40
Training: Tag A
Offen: nichts

HEALTHLOG-JSON-START
{"datum":"2026-11-13","mahlzeiten":[…],"protein_g":123,…}
HEALTHLOG-JSON-ENDE
```

Zahlen mit Dezimalpunkt, nur gesetzte Felder. Das Format ist eine
Schnittstelle zum Skill `health-os-debrief` (Abschnitt "Tageserfassung:
HealthLog-App"): nicht ändern, ohne den Skill nachzuziehen.

## Datenmodell (localStorage)

- `healthlog.days.v1`: Objekt Datum → Tag (Felder wie oben, dazu `datum`)
- `healthlog.settings.v1`: `ziel` (Protein-Untergrenze), `vorlagen`
  [{was, g}], `lastExport`, `schema`

Die Protein-Vorlagen im Seed sind eine Kopie aus dem Analyzer
(`einstellungen/protein`, Stand 14.11.2026) und greifen nur beim ersten
Start; danach pflegt Daniel sie in der App.
