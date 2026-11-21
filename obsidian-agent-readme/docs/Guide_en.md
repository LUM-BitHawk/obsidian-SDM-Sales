# Obsidian-Agent SDM – Guide

**Languages:** [Deutsch](Anleitung_de.md) · [English](Guide_en.md) · [Français](Guide_fr.md) · [Italiano](Guida_it.md) · [Español](Guia_es.md) · [Română](Ghid_ro.md)

Obsidian-Agent is a local web interface for **service delivery management and sales**. It reads your Obsidian vault and shows customers, IT assets with their lifecycle, opportunities, meetings and tasks in one place. It runs only on your computer at `http://127.0.0.1:5055`.

**Core principle:** the Markdown files in the vault are the single source of truth. The `planer.db` database is only an index and is rebuilt at every sync. You can keep working directly in Obsidian at any time.

---

## 1. Installation

**Requirements:** Python 3.10 or newer (Windows) or 3.9 or newer (macOS), plus an internet connection on first start. The agent downloads the Flask package then.

### Windows – `ObsidianAgent-SDM-Setup.exe`
1. Install Python from [python.org](https://www.python.org/downloads/windows/) and tick **«Add python.exe to PATH»**.
2. Unzip `ObsidianAgent-SDM-Setup-Windows.zip` and run `ObsidianAgent-SDM-Setup.exe`. No administrator rights are needed.
   If *«Windows protected your PC»* appears, click **More info → Run anyway**. The setup is not digitally signed.
3. The wizard guides you through the installation in five steps:
   - Choose the **language**.
   - **Install location and options:** the default is `%LOCALAPPDATA%\Programs\Obsidian-Agent`. Choose whether the agent starts automatically at sign-in and whether shortcuts are created in the Start menu and on the desktop.
   - **Obsidian vault:** choose your vault folder or tick «Set it later in the settings».
   - **Install:** the setup copies the program and sets up the Python environment, the vault and the daily sync (04:00, missed runs are caught up) without a console window.
   - **Finish:** with «Open Obsidian-Agent SDM» the interface opens in your browser.
4. Later, start the agent from the desktop shortcut, the Start menu entry **Obsidian-Agent SDM** or a bookmark to `http://127.0.0.1:5055`.

### macOS – `ObsidianAgent-SDM.pkg`
1. Install Python from [python.org](https://www.python.org/downloads/macos/) or run `brew install python` in Terminal.
2. Unzip `ObsidianAgent-SDM-Mac.zip` and open the package with **right-click → Open**. It is not signed. If macOS still blocks it: *System Settings → Privacy & Security → Open Anyway*.
3. The package installs the program to `~/Applications/Obsidian-Agent` and opens a Terminal window for the setup. There you select the vault and decide whether the agent starts automatically at login.
4. If the vault is in *Documents*, on the *Desktop* or in *iCloud Drive*, macOS may block the background sync. The script then shows which file to allow under *Full Disk Access*.
5. To start: double-click `~/Applications/Obsidian-Agent/mac/starten_mac.command` or use a bookmark to `http://127.0.0.1:5055`.

### Without setup (ZIP) and Linux
Unzip the ZIP (not into the vault) and start it:
- Windows: `installieren.bat`
- macOS: `mac/installieren_mac.command`
- Linux: `./start.sh`

You can pass the language as a parameter, e.g. `installieren.bat en`. For the daily sync on Linux, add this via `crontab -e`: `0 4 * * * cd /path/obsidian-agent && .venv/bin/python agent.py sync --geplant`

---

## 2. The interface

| Page | Purpose |
|---|---|
| **Today** | Tasks due, lifecycle actions without an opportunity, opportunities with an overdue close, upcoming meetings, customers with status red |
| **Customers** | Tiles with SLA, status, next lifecycle case and pipeline; a click opens the customer profile with a 24-month lifecycle bar, assets, opportunities, meetings, contacts, tasks and escalations |
| **Lifecycle** | All assets, licences and contracts with phase and notice period; «none – create?» creates a linked opportunity directly |
| **Pipeline** | Kanban by phase with total and weighted volume; change the phase directly on the card |
| **Meetings** | Regular meetings, service reviews, QBRs etc.; new minutes automatically list the customer's lifecycle topics and open tasks |
| **Tasks** | All checkbox tasks from the vault; tick them off directly in the list |
| **Search** | Full-text search across the whole vault |
| **Settings** | Language, vault path, folder names, currency, sync time, rewrite templates |

Switch the **language** at the bottom left of the navigation or in the settings.

---

## 3. Vault structure

At the first sync the agent creates these folders if they are missing. Their names follow the language. Existing folders are never renamed.

| Folder (English) | Content |
|---|---|
| `00_Cockpit` | `Today.md` (rewritten at every sync) and `Tasks.md` |
| `01_Customers` | one note per customer |
| `02_Contacts` | contact persons |
| `03_Meetings` | minutes |
| `04_Service_Escalations` | incidents and escalations |
| `05_Sales_Opportunities` | opportunities |
| `06_IT_Assets_Lifecycle` | hardware, software, licences, contracts, certificates |
| `99_Templates` | templates for the Obsidian core plugin «Templates» |

The agent recognises notes by the `typ` property, not by the folder, so you can file them differently.

### Properties
The property names are the same in every language so that mixed teams can share one vault. Allowed values are in brackets. Free spellings such as «Green», «Grün» or «Lifecycle management» are recognised as well.

| `typ` | Main properties |
|---|---|
| `kunde` (customer) | `sla_level` (gold, silber, bronze) · `status` (gruen, gelb, rot) · `vertragsende` (contract end) · `kuendigungsfrist_monate` (notice period in months) · `jahresumsatz` (annual revenue) · `sdm_lead` · `account_manager` |
| `kontakt` (contact) | `kunde` · `funktion` · `rolle` (entscheider, einfluss, anwender, einkauf) · `stimmung` (positiv, neutral, kritisch) · `loeschen_nach` (delete after) |
| `asset` | `kunde` · `kategorie` (hardware, netzwerk, betriebssystem, applikation, lizenz, vertrag, zertifikat) · `produkt_version` · `end_of_support` · `end_of_life` · `vertragsende` · `kuendigungsfrist_monate` · `esu_bis` · `kritikalitaet` (hoch, mittel, niedrig) · `betriebsstatus` (planung, betrieb, auslauf, ausser_betrieb) · `verknuepfte_opportunity` |
| `opportunity` | `kunde` · `phase` (qualifizierung, angebot, verhandlung, gewonnen, verloren) · `wahrscheinlichkeit` (probability) · `volumen` · `abschluss_geplant` (planned close) · `quelle` (lifecycle, service, bestandskunde, neukunde) · `verknuepftes_asset` |
| `meeting` | `kunde` · `art` (jour_fixe, service_review, qbr, call, workshop) · `datum` · `teilnehmer` |
| `eskalation` (escalation) | `kunde` · `prioritaet` (p1, p2, p3) · `status` (offen, geschlossen) |

Further rules:
- **Date format:** `YYYY-MM-DD`.
- **Linking a customer:** `kunde: "[[Customer_Example_Ltd]]"`. The plain name (`kunde: Example Ltd`) works as well.
- **Avoid comments in YAML** (`# …`): Obsidian deletes them when properties are edited.

### Lifecycle phases
The agent calculates an **action date**. This is the earlier of the two dates:
- end of support or end of life
- contract end minus notice period

| Phase | Action date within | What to do now |
|---|---|---|
| Strategy | ≤ 12 months | raise it in the service review, start budget planning |
| Proposal | ≤ 6 months | concept with delivery, prepare a proposal |
| Implementation | ≤ 3 months | order, meet deadlines, plan the migration window |
| Deadline missed / Expired | passed | clarify immediately; with `esu_bis` it shows «ESU until …» |

Assets with `kritikalitaet: hoch` (high) move up one phase.

### Tasks
Tasks are ordinary checkbox lines in any note, compatible with the Obsidian **Tasks** plugin:

```
- [ ] Send proposal 📅 2026-12-01
```

New tasks created in the interface go to the «✅ Tasks & next steps» section of the customer note. Ticking a task sets `[x]` and `✅ date`.

---

## 4. Recommended Obsidian settings
- **Core plugins:** *Properties* and *Templates*. Set the template folder to `99_Templates`.
- **Optional:** the community plugin **Tasks** for task lists inside Obsidian and the core plugin **Bases** for your own tables. Dataview is no longer needed.

---

## 5. Updating from the earlier version (project version)
- The database is rebuilt automatically. Your vault stays unchanged, and old folders (projects, wiki, DevOps …) remain in place.
- Old lifecycle entries (`LCM_*.md` or `typ: lcm`) are recognised as assets. The `projekt` field is read as the customer when a customer with that name exists.
- The scheduled tasks keep their names and point to the new program folder after installation.
- To keep the old `config.json` (vault path, port), copy it into the new program folder before running the setup.

## 6. Uninstall and moving
- **Windows:** *Settings → Apps → Obsidian-Agent SDM → Uninstall*, or `deinstallieren.bat`.
- **macOS:** `mac/deinstallieren_mac.command`.

Both stop the agent, remove the autostart and the daily sync, and back up `config.json` and `planer.db` to the desktop (`obsidian-agent-backup_<date>`). **The vault is never changed.**

**Moving:** install at the new location and copy `config.json` from the backup into the program folder.

## 7. Data protection
Contact notes contain personal data. Record only factual, business-relevant details, set `loeschen_nach` and follow your employer's rules (GDPR / Swiss FADP). A shared vault has no fine-grained permissions.

## 8. Troubleshooting

| Problem | Solution |
|---|---|
| Page does not load | Run `start.bat` or `mac/starten_mac.command`. Log: `%LOCALAPPDATA%\obsidian-agent\gui.log` or `~/Library/Logs/obsidian-agent/` |
| «Vault folder not found» | Correct the path in *Settings* |
| An asset does not appear | Does the note have `typ: asset`? Is it outside the template folder? Then click **Refresh now** |
| A task cannot be ticked | The note was changed in the meantime: click **Refresh now** and try again |
| Sync time or port changed | Run the setup again (`installieren.bat` or `mac/installieren_mac.command`) |
