# Obsidian-Agent SDM – Ghid

**Limbi:** [Deutsch](Anleitung_de.md) · [English](Guide_en.md) · [Français](Guide_fr.md) · [Italiano](Guida_it.md) · [Español](Guia_es.md) · [Română](Ghid_ro.md)

Obsidian-Agent este o interfață web locală pentru **Service Delivery Management și vânzări**. Citește seiful (vault-ul) Obsidian și afișează într-un singur loc clienții, activele IT cu ciclul lor de viață, oportunitățile, întâlnirile și sarcinile. Rulează doar pe computerul dumneavoastră, la adresa `http://127.0.0.1:5055`.

**Principiu de bază:** fișierele Markdown din seif sunt singura sursă de adevăr. Baza de date `planer.db` este doar un index și se reconstruiește la fiecare sincronizare. Puteți lucra oricând direct în Obsidian.

---

## 1. Instalare

**Cerințe:** Python 3.10 sau mai nou (Windows) sau 3.9 sau mai nou (macOS), plus o conexiune la internet la prima pornire. Atunci agentul descarcă pachetul Flask.

### Windows – `ObsidianAgent-SDM-Setup.exe`
1. Instalați Python de pe [python.org](https://www.python.org/downloads/windows/) și bifați **«Add python.exe to PATH»**.
2. Dezarhivați `ObsidianAgent-SDM-Setup-Windows.zip` și porniți `ObsidianAgent-SDM-Setup.exe`. Nu sunt necesare drepturi de administrator.
   Dacă apare *«Windows a protejat PC-ul»*, faceți clic pe **Mai multe informații → Executare oricum**. Programul de instalare nu este semnat digital.
3. Asistentul vă ghidează în cinci pași:
   - Alegeți **limba**.
   - **Folder de instalare și opțiuni:** propunerea este `%LOCALAPPDATA%\Programs\Obsidian-Agent`. Alegeți dacă agentul pornește automat la autentificare și dacă se creează scurtături în meniul Start și pe desktop.
   - **Seiful Obsidian:** alegeți folderul seifului sau bifați «Setați mai târziu în setări».
   - **Instalare:** programul este copiat, iar mediul Python, seiful și sincronizarea zilnică (04:00, rulările ratate sunt recuperate) se configurează fără fereastră de consolă.
   - **Finalizare:** cu «Deschide Obsidian-Agent SDM» interfața se deschide în browser.
4. Ulterior, porniți agentul din scurtătura de pe desktop, din meniul Start **Obsidian-Agent SDM** sau dintr-un marcaj către `http://127.0.0.1:5055`.

### macOS – `ObsidianAgent-SDM.pkg`
1. Instalați Python de pe [python.org](https://www.python.org/downloads/macos/) sau rulați `brew install python` în Terminal.
2. Dezarhivați `ObsidianAgent-SDM-Mac.zip` și deschideți pachetul cu **clic dreapta → Deschide**. Nu este semnat. Dacă macOS îl blochează totuși: *Setări sistem → Confidențialitate și securitate → Deschide oricum*.
3. Pachetul instalează aplicația în `~/Applications/Obsidian-Agent` și deschide o fereastră Terminal pentru configurare. Acolo alegeți seiful și decideți dacă agentul pornește automat la autentificare.
4. Dacă seiful se află în *Documente*, pe *Birou* sau în *iCloud Drive*, macOS poate bloca sincronizarea în fundal. Scriptul arată atunci ce fișier trebuie permis la *Acces complet la disc*.
5. Pornire: dublu clic pe `~/Applications/Obsidian-Agent/mac/starten_mac.command` sau un marcaj către `http://127.0.0.1:5055`.

### Fără program de instalare (ZIP) și Linux
Dezarhivați ZIP-ul (nu în seif) și porniți:
- Windows: `installieren.bat`
- macOS: `mac/installieren_mac.command`
- Linux: `./start.sh`

Limba se poate transmite ca parametru, de ex. `installieren.bat ro`. Pentru sincronizarea zilnică pe Linux, adăugați cu `crontab -e`: `0 4 * * * cd /cale/obsidian-agent && .venv/bin/python agent.py sync --geplant`

---

## 2. Interfața

| Pagină | Rol |
|---|---|
| **Astăzi** | Sarcini scadente, acțiuni de ciclu de viață fără oportunitate, oportunități cu închidere depășită, întâlniri următoare, clienți cu stare roșie |
| **Clienți** | Carduri cu SLA, stare, următorul caz de ciclu de viață și pipeline; un clic deschide fișa clientului cu bara ciclului de viață pe 24 de luni, active, oportunități, întâlniri, contacte, sarcini și escaladări |
| **Ciclu de viață** | Toate activele, licențele și contractele cu fază și preaviz; «niciuna – creați?» creează direct o oportunitate legată |
| **Pipeline** | Kanban pe faze cu volum total și ponderat; faza se schimbă direct pe card |
| **Întâlniri** | Ședințe periodice, analize de servicii, QBR etc.; un proces-verbal nou include automat temele de ciclu de viață și sarcinile deschise ale clientului |
| **Sarcini** | Toate sarcinile cu casetă de bifare din seif; se bifează direct în listă |
| **Căutare** | Căutare în text integral în tot seiful |
| **Setări** | Limbă, calea seifului, numele folderelor, monedă, ora sincronizării, rescrierea șabloanelor |

**Limba** se schimbă în stânga jos, în navigare, sau în setări.

---

## 3. Structura seifului

La prima sincronizare, agentul creează aceste foldere dacă lipsesc. Numele urmează limba aleasă. Folderele existente nu sunt redenumite niciodată.

| Folder (română) | Conținut |
|---|---|
| `00_Cockpit` | `Astăzi.md` (rescris la fiecare sincronizare) și `Sarcini.md` |
| `01_Clienti` | o notiță pentru fiecare client |
| `02_Contacte` | persoane de contact |
| `03_Intalniri` | procese-verbale |
| `04_Service_Escaladari` | incidente și escaladări |
| `05_Vanzari_Oportunitati` | oportunități |
| `06_Active_IT_Ciclu_de_viata` | hardware, software, licențe, contracte, certificate |
| `99_Sabloane` | șabloane pentru pluginul de bază «Șabloane» din Obsidian |

Agentul recunoaște notițele după proprietatea `typ`, nu după folder. Le puteți deci organiza și altfel.

### Proprietăți
Numele proprietăților sunt aceleași în toate limbile, pentru ca echipele mixte să poată folosi același seif. Valorile permise sunt între paranteze. Sunt recunoscute și variante libere precum «Verde», «Green» sau «Lifecycle management».

| `typ` | Proprietăți principale |
|---|---|
| `kunde` (client) | `sla_level` (gold, silber, bronze) · `status` (gruen, gelb, rot) · `vertragsende` (sfârșitul contractului) · `kuendigungsfrist_monate` (preaviz în luni) · `jahresumsatz` (cifră de afaceri anuală) · `sdm_lead` · `account_manager` |
| `kontakt` (contact) | `kunde` · `funktion` · `rolle` (entscheider, einfluss, anwender, einkauf) · `stimmung` (positiv, neutral, kritisch) · `loeschen_nach` (șterge după) |
| `asset` (activ) | `kunde` · `kategorie` (hardware, netzwerk, betriebssystem, applikation, lizenz, vertrag, zertifikat) · `produkt_version` · `end_of_support` · `end_of_life` · `vertragsende` · `kuendigungsfrist_monate` · `esu_bis` · `kritikalitaet` (hoch, mittel, niedrig) · `betriebsstatus` (planung, betrieb, auslauf, ausser_betrieb) · `verknuepfte_opportunity` |
| `opportunity` | `kunde` · `phase` (qualifizierung, angebot, verhandlung, gewonnen, verloren) · `wahrscheinlichkeit` (probabilitate) · `volumen` · `abschluss_geplant` (închidere planificată) · `quelle` (lifecycle, service, bestandskunde, neukunde) · `verknuepftes_asset` |
| `meeting` (întâlnire) | `kunde` · `art` (jour_fixe, service_review, qbr, call, workshop) · `datum` · `teilnehmer` |
| `eskalation` (escaladare) | `kunde` · `prioritaet` (p1, p2, p3) · `status` (offen, geschlossen) |

Alte reguli:
- **Format dată:** `AAAA-LL-ZZ`.
- **Legarea unui client:** `kunde: "[[Client_Exemplu_SRL]]"`. Funcționează și doar numele (`kunde: Exemplu SRL`).
- **Evitați comentariile în YAML** (`# …`): Obsidian le șterge când editați proprietățile.

### Fazele ciclului de viață
Agentul calculează un **termen de acțiune**. Acesta este data cea mai apropiată dintre:
- sfârșitul suportului sau sfârșitul de viață
- sfârșitul contractului minus preavizul

| Fază | Termen de acțiune în | Ce trebuie făcut acum |
|---|---|---|
| Strategie | ≤ 12 luni | discutați în analiza serviciilor, inițiați planificarea bugetului |
| Ofertă | ≤ 6 luni | concept cu echipa de livrare, pregătiți oferta |
| Implementare | ≤ 3 luni | comandați, respectați termenele, planificați fereastra de migrare |
| Termen ratat / Expirat | depășit | clarificați imediat; cu `esu_bis` se afișează «ESU până la …» |

Activele cu `kritikalitaet: hoch` (ridicată) avansează cu o fază.

### Sarcini
Sarcinile sunt rânduri obișnuite cu casetă de bifare în orice notiță, compatibile cu pluginul Obsidian **Tasks**:

```
- [ ] Trimite oferta 📅 2026-12-01
```

Sarcinile noi create din interfață se adaugă în secțiunea «✅ Sarcini & pașii următori» a notiței clientului. Bifarea unei sarcini scrie `[x]` și `✅ data`.

---

## 4. Setări Obsidian recomandate
- **Pluginuri de bază:** *Proprietăți* și *Șabloane*. Setați folderul de șabloane la `99_Sabloane`.
- **Opțional:** pluginul comunității **Tasks** pentru liste de sarcini în Obsidian și pluginul de bază **Bases** pentru tabele proprii. Dataview nu mai este necesar.

---

## 5. Actualizare de la versiunea anterioară (versiunea cu proiecte)
- Baza de date se reconstruiește automat. Seiful rămâne neschimbat, iar folderele vechi (proiecte, wiki, DevOps …) rămân pe loc.
- Intrările vechi de ciclu de viață (`LCM_*.md` sau `typ: lcm`) sunt recunoscute ca active. Câmpul `projekt` este citit drept client dacă există un client cu acest nume.
- Sarcinile programate își păstrează numele și, după instalare, indică noul folder al programului.
- Pentru a păstra vechiul `config.json` (calea seifului, portul), copiați-l în noul folder al programului înainte de configurare.

## 6. Dezinstalare și mutare
- **Windows:** *Setări → Aplicații → Obsidian-Agent SDM → Dezinstalare* sau `deinstallieren.bat`.
- **macOS:** `mac/deinstallieren_mac.command`.

Ambele opresc agentul, elimină pornirea automată și sincronizarea zilnică și salvează `config.json` și `planer.db` pe desktop (`obsidian-agent-backup_<dată>`). **Seiful nu este modificat niciodată.**

**Mutare:** instalați în noua locație și copiați `config.json` din copia de rezervă în folderul programului.

## 7. Protecția datelor
Notițele de contact conțin date personale. Înregistrați doar informații obiective, necesare profesional, setați `loeschen_nach` și respectați regulile angajatorului (GDPR). Un seif comun nu oferă permisiuni detaliate.

## 8. Depanare

| Problemă | Soluție |
|---|---|
| Pagina nu se încarcă | Porniți `start.bat` sau `mac/starten_mac.command`. Jurnal: `%LOCALAPPDATA%\obsidian-agent\gui.log` sau `~/Library/Logs/obsidian-agent/` |
| «Folderul seifului nu a fost găsit» | Corectați calea în *Setări* |
| Un activ nu apare | Notița are `typ: asset`? Se află în afara folderului de șabloane? Apoi apăsați **Actualizează acum** |
| O sarcină nu poate fi bifată | Notița a fost modificată între timp: apăsați **Actualizează acum** și încercați din nou |
| Ora sincronizării sau portul au fost schimbate | Rulați din nou configurarea (`installieren.bat` sau `mac/installieren_mac.command`) |
