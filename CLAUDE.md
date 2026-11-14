# HealthLog

Offline-Android-App zur Tageserfassung für Daniels Health OS. Reine Web-App
in einer Datei, die per [apk-builder](../apk-builder) zur APK wird. Felder,
Exportformat und Datenmodell stehen in [README.md](README.md), Versionen in
[CHANGELOG.md](CHANGELOG.md).

## Build & Test

Kein Bundler, kein Build-Step. Testen über http: Testserver `health-logger`
aus der Workspace-`.claude/launch.json` (Port 8150, `node tools/serve.mjs`),
dann `http://localhost:8150/web/`. `?debug=1` führt den Selbsttest aus
(Konsole und Zeile auf der Startseite, auch per `hlTests()`). Er muss
komplett `OK` zeigen.

APK bauen, **immer über den eigenen Wrapper** (baut die Brücke `HlAndroid`
ein: `android-src/FileBridge.java` + Patch in `MainActivity`):

```powershell
cd "D:\claude code projects\health-logger"
.\build.ps1 -VersionName "1.1" -VersionCode 2 -Release   # signiert -> dist\healthlog-<v>.apk
```

Nur auf dem Gerät prüfbar: native Zwischenablage (`copyText`), Teilen-Menü,
Datei-Backup, Zeit- und Zahleneingabe mit der Android-Tastatur.

## Konventionen

- Alles in `web/index.html`, kein Framework, kein Netzwerk.
- Feldnamen = Feldnamen des Health OS Analyzer. Neue Felder erst mit dem
  Skill `health-os-debrief` abgleichen (Kopie zum Bearbeiten in
  `..\health-os-skills\`), dann in `FLAGS`/`dayJson`/`logText`/`openItems`
  und im Selbsttest ergänzen.
- `openItems()` bildet "Lücken bestimmen" aus dem Skill nach. Ändert sich
  dort die Pflichtliste, hier nachziehen.
- Tippen statt Schreiben: jede häufige Eingabe ist ein Tap. Eingabefelder
  speichern bei `change` ohne Neuzeichnen, damit der Fokus bleibt.
- Kein `confirm()`/`alert()`: Der WebView hat keinen WebChromeClient.
- Seed-Vorlagen greifen nur beim ersten Start; Änderungen für ein
  installiertes Gerät brauchen einen Schritt in `migrate()`.
- Die App empfiehlt nichts und bewertet nichts (das macht der Health-OS-Chat).

## Aktueller Stand

v1.0 (code 1, 2026-11-14): Tagesansicht mit Protein-Vorlagen, Ja/Nein-Chips
(drei Zustände), Werten, Zeiten, Training, Notiz, Liste der offenen Punkte,
"Für Health-OS kopieren" und "Teilen", Verlauf der letzten 14 Tage,
Einstellungen (Untergrenze, Vorlagen, Backup). Selbsttest 23/23, im Browser
durchgeklickt. Signierte APK `dist/healthlog-1.0.apk`, **auf dem Gerät noch
nicht getestet**. Der Skill-Abschnitt für das HEALTHLOG-Format ist
geschrieben (`..\health-os-skills\health-os-debrief.skill`); aktiv wird er
erst, wenn Daniel ihn in seinem Claude-Konto hochlädt.
