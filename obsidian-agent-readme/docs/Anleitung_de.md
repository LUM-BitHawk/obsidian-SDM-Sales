# Obsidian-Agent SDM – Anleitung

**Sprachen:** [Deutsch](Anleitung_de.md) · [English](Guide_en.md) · [Français](Guide_fr.md) · [Italiano](Guida_it.md) · [Español](Guia_es.md) · [Română](Ghid_ro.md)

Der Obsidian-Agent ist eine lokale Weboberfläche für **Service Delivery Management und Vertrieb**. Er liest deinen Obsidian-Vault und zeigt Kunden, IT-Assets mit ihrem Lebenszyklus, Opportunities, Meetings und Aufgaben an einem Ort. Er läuft nur auf deinem Computer unter `http://127.0.0.1:5055`.

**Grundprinzip:** Die Markdown-Dateien im Vault sind die einzige Wahrheit. Die Datenbank `planer.db` ist nur ein Index und wird bei jedem Sync neu aufgebaut. Du kannst jederzeit direkt in Obsidian arbeiten.

---

## 1. Installation

**Voraussetzung:** Python 3.10 oder neuer (Windows) bzw. 3.9 oder neuer (macOS) und eine Internetverbindung beim ersten Start. Der Agent lädt dabei das Paket Flask herunter.

### Windows – `ObsidianAgent-SDM-Setup.exe`
1. Python von [python.org](https://www.python.org/downloads/windows/) installieren und dabei **«Add python.exe to PATH»** anhaken.
2. `ObsidianAgent-SDM-Setup-Windows.zip` entpacken und `ObsidianAgent-SDM-Setup.exe` starten. Administratorrechte sind nicht nötig.
   Erscheint *«Der Computer wurde durch Windows geschützt»*, auf **Weitere Informationen → Trotzdem ausführen** klicken. Das Setup ist nicht digital signiert.
3. Der Assistent führt in fünf Schritten durch die Installation:
   - **Sprache** wählen.
   - **Zielverzeichnis und Optionen:** Vorgeschlagen ist `%LOCALAPPDATA%\Programs\Obsidian-Agent`. Dazu wählst du, ob der Agent bei der Anmeldung automatisch startet und ob Verknüpfungen im Startmenü und auf dem Desktop entstehen.
   - **Obsidian-Vault:** Ordner deines Vaults wählen oder «Später in den Einstellungen festlegen» anhaken.
   - **Installieren:** Das Setup kopiert das Programm und richtet Python-Umgebung, Vault und den täglichen Sync (04:00, verpasste Läufe werden nachgeholt) ohne Konsolenfenster ein.
   - **Fertig stellen:** Mit «Obsidian-Agent SDM öffnen» startet die Oberfläche im Browser.
4. Später startest du den Agent über die Desktop-Verknüpfung, das Startmenü **Obsidian-Agent SDM** oder ein Lesezeichen auf `http://127.0.0.1:5055`.

### macOS – `ObsidianAgent-SDM.pkg`
1. Python von [python.org](https://www.python.org/downloads/macos/) installieren oder im Terminal `brew install python` ausführen.
2. `ObsidianAgent-SDM-Mac.zip` entpacken und das Paket mit **Rechtsklick → Öffnen** starten. Es ist nicht signiert. Meldet macOS trotzdem eine Sperre: *Systemeinstellungen → Datenschutz & Sicherheit → Dennoch öffnen*.
3. Das Paket installiert das Programm nach `~/Applications/Obsidian-Agent` und öffnet ein Terminal-Fenster für die Einrichtung. Dort fragst du den Vault ab und entscheidest, ob der Agent beim Anmelden automatisch startet.
4. Liegt der Vault in *Dokumente*, auf dem *Schreibtisch* oder in *iCloud Drive*, kann macOS den Hintergrund-Sync blockieren. Das Skript zeigt dann, welche Datei du unter *Festplattenvollzugriff* freigeben musst.
5. Starten: `~/Applications/Obsidian-Agent/mac/starten_mac.command` per Doppelklick oder ein Lesezeichen auf `http://127.0.0.1:5055`.

### Ohne Setup (ZIP) und Linux
ZIP entpacken (nicht in den Vault) und starten:
- Windows: `installieren.bat`
- macOS: `mac/installieren_mac.command`
- Linux: `./start.sh`

Die Sprache kannst du als Parameter mitgeben, z.B. `installieren.bat fr`. Für den täglichen Sync unter Linux trägst du per `crontab -e` ein: `0 4 * * * cd /pfad/obsidian-agent && .venv/bin/python agent.py sync --geplant`

---

## 2. Die Oberfläche

| Seite | Wofür |
|---|---|
| **Heute** | Fällige Aufgaben, Lifecycle-Aktionen ohne Opportunity, Opportunities mit überfälligem Abschluss, nächste Meetings, Kunden mit Status rot |
| **Kunden** | Kacheln mit SLA, Status, nächstem Lifecycle-Fall und Pipeline; Klick öffnet den Steckbrief mit Lifecycle-Leiste (24 Monate), Assets, Opportunities, Meetings, Kontakten, Aufgaben und Eskalationen |
| **Lifecycle** | Alle Assets, Lizenzen und Verträge mit Phase und Kündigungsfrist; «keine – anlegen?» erstellt direkt eine verknüpfte Opportunity |
| **Pipeline** | Kanban nach Phase mit Summe und gewichtetem Volumen; die Phase änderst du direkt auf der Karte |
| **Meetings** | Jour Fixe, Service Review, QBR usw.; ein neues Protokoll enthält automatisch die Lifecycle-Themen und offenen Aufgaben des Kunden |
| **Aufgaben** | Alle Checkbox-Aufgaben aus dem Vault; abhaken direkt in der Liste |
| **Suche** | Volltext über den ganzen Vault |
| **Einstellungen** | Sprache, Vault-Pfad, Ordnernamen, Währung, Sync-Zeit, Vorlagen neu schreiben |

Die **Sprache** wechselst du unten links in der Navigation oder in den Einstellungen.

---

## 3. Ablage im Vault

Beim ersten Sync legt der Agent diese Ordner an, sofern sie fehlen. Die Namen richten sich nach der Sprache. Bestehende Ordner werden nie umbenannt.

| Ordner (Deutsch) | Inhalt |
|---|---|
| `00_Cockpit` | `Heute.md` (wird bei jedem Sync neu geschrieben) und `Aufgaben.md` |
| `01_Kunden` | eine Notiz pro Kunde |
| `02_Kontakte` | Ansprechpartner |
| `03_Meetings` | Protokolle |
| `04_Service_Eskalationen` | Incidents und Eskalationen |
| `05_Sales_Chancen` | Opportunities |
| `06_IT_Assets_Lifecycle` | Hardware, Software, Lizenzen, Verträge, Zertifikate |
| `99_Vorlagen` | Vorlagen für das Obsidian-Kernplugin «Vorlagen» |

Der Agent erkennt Notizen an der Eigenschaft `typ`, nicht am Ordner. Du kannst also auch anders ablegen.

### Eigenschaften (Properties)
Die Eigenschaften sind in allen Sprachen gleich, damit gemischte Teams einen Vault teilen können. Erlaubte Werte stehen in Klammern. Freie Schreibweisen wie «Grün», «Green» oder «Lifecycle-Management» werden ebenfalls erkannt.

| `typ` | Wichtige Eigenschaften |
|---|---|
| `kunde` | `sla_level` (gold, silber, bronze) · `status` (gruen, gelb, rot) · `vertragsende` · `kuendigungsfrist_monate` · `jahresumsatz` · `sdm_lead` · `account_manager` |
| `kontakt` | `kunde` · `funktion` · `rolle` (entscheider, einfluss, anwender, einkauf) · `stimmung` (positiv, neutral, kritisch) · `loeschen_nach` |
| `asset` | `kunde` · `kategorie` (hardware, netzwerk, betriebssystem, applikation, lizenz, vertrag, zertifikat) · `produkt_version` · `end_of_support` · `end_of_life` · `vertragsende` · `kuendigungsfrist_monate` · `esu_bis` · `kritikalitaet` (hoch, mittel, niedrig) · `betriebsstatus` (planung, betrieb, auslauf, ausser_betrieb) · `verknuepfte_opportunity` |
| `opportunity` | `kunde` · `phase` (qualifizierung, angebot, verhandlung, gewonnen, verloren) · `wahrscheinlichkeit` · `volumen` · `abschluss_geplant` · `quelle` (lifecycle, service, bestandskunde, neukunde) · `verknuepftes_asset` |
| `meeting` | `kunde` · `art` (jour_fixe, service_review, qbr, call, workshop) · `datum` · `teilnehmer` |
| `eskalation` | `kunde` · `prioritaet` (p1, p2, p3) · `status` (offen, geschlossen) |

Weitere Regeln:
- **Datumsformat:** `JJJJ-MM-TT`.
- **Kunde verknüpfen:** `kunde: "[[Kunde_Muster_AG]]"`. Der Name allein (`kunde: Muster AG`) funktioniert auch.
- **Kommentare im YAML** (`# …`) vermeiden: Obsidian löscht sie beim Bearbeiten der Eigenschaften.

### Lifecycle-Phasen
Der Agent berechnet einen **Handlungstermin**. Das ist das frühere der beiden Daten:
- End of Support bzw. End of Life
- Vertragsende minus Kündigungsfrist

| Phase | Handlungstermin in | Was jetzt zu tun ist |
|---|---|---|
| Strategie | ≤ 12 Monaten | im Service Review ansprechen, Budget anstossen |
| Angebot | ≤ 6 Monaten | Konzept mit Delivery, Angebot erstellen |
| Umsetzung | ≤ 3 Monaten | bestellen, Fristen wahren, Migrationsfenster planen |
| Frist verpasst / Abgelaufen | überschritten | sofort klären; mit `esu_bis` wird «ESU bis …» angezeigt |

Assets mit `kritikalitaet: hoch` rücken eine Stufe früher.

### Aufgaben
Aufgaben sind normale Checkbox-Zeilen in beliebigen Notizen, kompatibel mit dem Obsidian-Plugin **Tasks**:

```
- [ ] Angebot senden 📅 2026-12-01
```

Neue Aufgaben aus der Oberfläche landen im Abschnitt «✅ Aufgaben & Next Steps» der Kundennotiz. Abhaken setzt `[x]` und `✅ Datum`.

---

## 4. Empfohlene Obsidian-Einstellungen
- **Kernplugins:** *Eigenschaften* (Properties) und *Vorlagen*. Den Vorlagen-Ordner auf `99_Vorlagen` setzen.
- **Optional:** Community-Plugin **Tasks** für Aufgabenlisten in Obsidian und das Kernplugin **Bases** für eigene Tabellen. Dataview ist nicht mehr nötig.

---

## 5. Update von der früheren Version (Projekt-Version)
- Die Datenbank wird automatisch neu aufgebaut. Dein Vault bleibt unverändert, alte Ordner (Projekte, Wiki, DevOps …) bleiben liegen.
- Alte LifeCycle-Einträge (`LCM_*.md` bzw. `typ: lcm`) werden als Assets erkannt. Das Feld `projekt` wird als Kunde gelesen, wenn ein Kunde mit diesem Namen existiert.
- Die geplanten Aufgaben behalten ihre Namen und zeigen nach der Installation auf den neuen Programmordner.
- Willst du die alte `config.json` (Vault-Pfad, Port) übernehmen, kopiere sie vor der Einrichtung in den neuen Programmordner.

## 6. Deinstallation und Umzug
- **Windows:** *Einstellungen → Apps → Obsidian-Agent SDM → Deinstallieren* oder `deinstallieren.bat`.
- **macOS:** `mac/deinstallieren_mac.command`.

Beide beenden den Agent, entfernen Autostart und Tages-Sync und sichern `config.json` und `planer.db` auf den Desktop (`obsidian-agent-backup_<Datum>`). **Der Vault wird nie verändert.**

**Umzug:** am neuen Ort installieren und `config.json` aus der Sicherung in den Programmordner kopieren.

## 7. Datenschutz
Kontaktnotizen enthalten Personendaten. Erfasse nur geschäftlich nötige, sachliche Angaben, setze `loeschen_nach` und beachte die Regeln deines Arbeitgebers (revDSG/DSGVO). Ein gemeinsamer Vault kennt keine Feinberechtigungen.

## 8. Fehlerbehebung

| Problem | Lösung |
|---|---|
| Seite lädt nicht | `start.bat` bzw. `mac/starten_mac.command` starten. Protokoll: `%LOCALAPPDATA%\obsidian-agent\gui.log` bzw. `~/Library/Logs/obsidian-agent/` |
| «Vault-Ordner nicht gefunden» | Pfad unter *Einstellungen* korrigieren |
| Asset erscheint nicht | Hat die Notiz `typ: asset`? Liegt sie nicht im Vorlagen-Ordner? Dann **Jetzt aktualisieren** |
| Aufgabe lässt sich nicht abhaken | Die Notiz wurde zwischenzeitlich geändert: **Jetzt aktualisieren** und erneut versuchen |
| Sync-Zeit oder Port geändert | Einrichtung erneut ausführen (`installieren.bat` bzw. `mac/installieren_mac.command`) |
