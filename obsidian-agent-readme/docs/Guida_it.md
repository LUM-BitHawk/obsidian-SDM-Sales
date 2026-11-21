# Obsidian-Agent SDM – Guida

**Lingue:** [Deutsch](Anleitung_de.md) · [English](Guide_en.md) · [Français](Guide_fr.md) · [Italiano](Guida_it.md) · [Español](Guia_es.md) · [Română](Ghid_ro.md)

Obsidian-Agent è un'interfaccia web locale per il **Service Delivery Management e le vendite**. Legge il tuo vault Obsidian e mostra in un unico posto clienti, asset IT con il loro ciclo di vita, opportunità, riunioni e attività. Funziona solo sul tuo computer, all'indirizzo `http://127.0.0.1:5055`.

**Principio di base:** i file Markdown del vault sono l'unica fonte di verità. Il database `planer.db` è solo un indice e viene ricostruito a ogni sincronizzazione. Puoi lavorare direttamente in Obsidian in qualsiasi momento.

---

## 1. Installazione

**Requisiti:** Python 3.10 o successivo (Windows) o 3.9 o successivo (macOS), e una connessione Internet al primo avvio. L'agent scarica in quel momento il pacchetto Flask.

### Windows – `ObsidianAgent-SDM-Setup.exe`
1. Installare Python da [python.org](https://www.python.org/downloads/windows/) spuntando **«Add python.exe to PATH»**.
2. Decomprimere `ObsidianAgent-SDM-Setup-Windows.zip` e avviare `ObsidianAgent-SDM-Setup.exe`. Non servono diritti di amministratore.
   Se compare *«PC protetto da Windows»*, fare clic su **Ulteriori informazioni → Esegui comunque**. Il programma di installazione non è firmato digitalmente.
3. La procedura guidata si svolge in cinque passi:
   - Scegliere la **lingua**.
   - **Cartella di installazione e opzioni:** la cartella proposta è `%LOCALAPPDATA%\Programs\Obsidian-Agent`. Scegliere se l'agent si avvia automaticamente all'accesso e se creare collegamenti nel menu Start e sul desktop.
   - **Vault Obsidian:** scegliere la cartella del vault oppure spuntare «Impostare più tardi nelle impostazioni».
   - **Installa:** il programma viene copiato e vengono configurati ambiente Python, vault e sincronizzazione giornaliera (04:00, le esecuzioni perse vengono recuperate) senza finestra della console.
   - **Fine:** con «Apri Obsidian-Agent SDM» l'interfaccia si apre nel browser.
4. In seguito si avvia l'agent dal collegamento sul desktop, dal menu Start **Obsidian-Agent SDM** o da un segnalibro su `http://127.0.0.1:5055`.

### macOS – `ObsidianAgent-SDM.pkg`
1. Installare Python da [python.org](https://www.python.org/downloads/macos/) oppure eseguire `brew install python` nel Terminale.
2. Decomprimere `ObsidianAgent-SDM-Mac.zip` e aprire il pacchetto con **clic destro → Apri**. Non è firmato. Se macOS lo blocca comunque: *Impostazioni di Sistema → Privacy e sicurezza → Apri comunque*.
3. Il pacchetto installa il software in `~/Applications/Obsidian-Agent` e apre una finestra del Terminale per la configurazione. Lì si sceglie il vault e si decide se l'agent si avvia automaticamente all'accesso.
4. Se il vault si trova in *Documenti*, sulla *Scrivania* o in *iCloud Drive*, macOS può bloccare la sincronizzazione in background. Lo script indica allora quale file abilitare in *Accesso completo al disco*.
5. Avvio: doppio clic su `~/Applications/Obsidian-Agent/mac/starten_mac.command` oppure un segnalibro su `http://127.0.0.1:5055`.

### Senza programma di installazione (ZIP) e Linux
Decomprimere lo ZIP (non nel vault) e avviare:
- Windows: `installieren.bat`
- macOS: `mac/installieren_mac.command`
- Linux: `./start.sh`

La lingua si può passare come parametro, ad es. `installieren.bat it`. Per la sincronizzazione giornaliera su Linux, aggiungere con `crontab -e`: `0 4 * * * cd /percorso/obsidian-agent && .venv/bin/python agent.py sync --geplant`

---

## 2. L'interfaccia

| Pagina | A cosa serve |
|---|---|
| **Oggi** | Attività in scadenza, azioni del ciclo di vita senza opportunità, opportunità con chiusura scaduta, prossime riunioni, clienti con stato rosso |
| **Clienti** | Riquadri con SLA, stato, prossimo caso del ciclo di vita e pipeline; un clic apre la scheda cliente con la barra del ciclo di vita a 24 mesi, asset, opportunità, riunioni, contatti, attività ed escalation |
| **Ciclo di vita** | Tutti gli asset, le licenze e i contratti con fase e preavviso; «nessuna – creare?» crea direttamente un'opportunità collegata |
| **Pipeline** | Kanban per fase con volume totale e ponderato; la fase si modifica direttamente sulla scheda |
| **Riunioni** | Riunioni periodiche, service review, QBR ecc.; un nuovo verbale riporta automaticamente i temi del ciclo di vita e le attività aperte del cliente |
| **Attività** | Tutte le attività con casella di controllo del vault; spuntarle direttamente nell'elenco |
| **Ricerca** | Ricerca a testo pieno in tutto il vault |
| **Impostazioni** | Lingua, percorso del vault, nomi delle cartelle, valuta, orario di sincronizzazione, riscrittura dei modelli |

La **lingua** si cambia in basso a sinistra nella navigazione o nelle impostazioni.

---

## 3. Struttura del vault

Alla prima sincronizzazione l'agent crea queste cartelle, se mancano. I nomi seguono la lingua scelta. Le cartelle esistenti non vengono mai rinominate.

| Cartella (italiano) | Contenuto |
|---|---|
| `00_Cockpit` | `Oggi.md` (riscritto a ogni sincronizzazione) e `Attività.md` |
| `01_Clienti` | una nota per cliente |
| `02_Contatti` | referenti |
| `03_Riunioni` | verbali |
| `04_Service_Escalation` | incidenti ed escalation |
| `05_Vendite_Opportunita` | opportunità |
| `06_Asset_IT_Ciclo_di_vita` | hardware, software, licenze, contratti, certificati |
| `99_Modelli` | modelli per il plugin principale «Modelli» di Obsidian |

L'agent riconosce le note dalla proprietà `typ`, non dalla cartella. Puoi quindi archiviarle anche altrove.

### Proprietà
I nomi delle proprietà sono uguali in tutte le lingue, così team misti possono condividere un vault. I valori ammessi sono tra parentesi. Vengono riconosciute anche grafie libere come «Verde», «Green» o «Lifecycle management».

| `typ` | Proprietà principali |
|---|---|
| `kunde` (cliente) | `sla_level` (gold, silber, bronze) · `status` (gruen, gelb, rot) · `vertragsende` (fine contratto) · `kuendigungsfrist_monate` (preavviso in mesi) · `jahresumsatz` (fatturato annuo) · `sdm_lead` · `account_manager` |
| `kontakt` (contatto) | `kunde` · `funktion` · `rolle` (entscheider, einfluss, anwender, einkauf) · `stimmung` (positiv, neutral, kritisch) · `loeschen_nach` (cancellare dopo) |
| `asset` | `kunde` · `kategorie` (hardware, netzwerk, betriebssystem, applikation, lizenz, vertrag, zertifikat) · `produkt_version` · `end_of_support` · `end_of_life` · `vertragsende` · `kuendigungsfrist_monate` · `esu_bis` · `kritikalitaet` (hoch, mittel, niedrig) · `betriebsstatus` (planung, betrieb, auslauf, ausser_betrieb) · `verknuepfte_opportunity` |
| `opportunity` | `kunde` · `phase` (qualifizierung, angebot, verhandlung, gewonnen, verloren) · `wahrscheinlichkeit` (probabilità) · `volumen` · `abschluss_geplant` (chiusura prevista) · `quelle` (lifecycle, service, bestandskunde, neukunde) · `verknuepftes_asset` |
| `meeting` (riunione) | `kunde` · `art` (jour_fixe, service_review, qbr, call, workshop) · `datum` · `teilnehmer` |
| `eskalation` (escalation) | `kunde` · `prioritaet` (p1, p2, p3) · `status` (offen, geschlossen) |

Altre regole:
- **Formato data:** `AAAA-MM-GG`.
- **Collegare un cliente:** `kunde: "[[Cliente_Esempio_SA]]"`. Funziona anche il solo nome (`kunde: Esempio SA`).
- **Evitare commenti nel YAML** (`# …`): Obsidian li elimina quando si modificano le proprietà.

### Fasi del ciclo di vita
L'agent calcola una **data di intervento**. È la più vicina tra queste due date:
- fine del supporto o fine vita
- fine del contratto meno il preavviso

| Fase | Data di intervento entro | Cosa fare ora |
|---|---|---|
| Strategia | ≤ 12 mesi | parlarne nella service review, avviare la pianificazione del budget |
| Offerta | ≤ 6 mesi | concetto con la delivery, preparare l'offerta |
| Attuazione | ≤ 3 mesi | ordinare, rispettare le scadenze, pianificare la finestra di migrazione |
| Termine mancato / Scaduto | superata | chiarire subito; con `esu_bis` compare «ESU fino al …» |

Gli asset con `kritikalitaet: hoch` (alta) avanzano di una fase.

### Attività
Le attività sono normali righe con casella di controllo in qualsiasi nota, compatibili con il plugin Obsidian **Tasks**:

```
- [ ] Inviare l'offerta 📅 2026-12-01
```

Le nuove attività create dall'interfaccia finiscono nella sezione «✅ Attività & prossimi passi» della nota cliente. Spuntare un'attività imposta `[x]` e `✅ data`.

---

## 4. Impostazioni Obsidian consigliate
- **Plugin principali:** *Proprietà* e *Modelli*. Impostare la cartella dei modelli su `99_Modelli`.
- **Facoltativo:** il plugin della community **Tasks** per gli elenchi di attività in Obsidian e il plugin principale **Bases** per tabelle personalizzate. Dataview non è più necessario.

---

## 5. Aggiornamento dalla versione precedente (versione progetti)
- Il database viene ricostruito automaticamente. Il vault resta invariato e le vecchie cartelle (progetti, wiki, DevOps …) rimangono al loro posto.
- Le vecchie voci del ciclo di vita (`LCM_*.md` o `typ: lcm`) vengono riconosciute come asset. Il campo `projekt` viene letto come cliente se esiste un cliente con quel nome.
- Le attività pianificate mantengono il loro nome e dopo l'installazione puntano alla nuova cartella del programma.
- Per mantenere il vecchio `config.json` (percorso del vault, porta), copiarlo nella nuova cartella del programma prima della configurazione.

## 6. Disinstallazione e trasloco
- **Windows:** *Impostazioni → App → Obsidian-Agent SDM → Disinstalla*, oppure `deinstallieren.bat`.
- **macOS:** `mac/deinstallieren_mac.command`.

Entrambi arrestano l'agent, rimuovono l'avvio automatico e la sincronizzazione giornaliera e salvano `config.json` e `planer.db` sul desktop (`obsidian-agent-backup_<data>`). **Il vault non viene mai modificato.**

**Trasloco:** installare nella nuova posizione e copiare `config.json` dal backup nella cartella del programma.

## 7. Protezione dei dati
Le note dei contatti contengono dati personali. Registrare solo informazioni oggettive e professionalmente necessarie, impostare `loeschen_nach` e rispettare le regole del proprio datore di lavoro (GDPR / nLPD). Un vault condiviso non offre autorizzazioni granulari.

## 8. Risoluzione dei problemi

| Problema | Soluzione |
|---|---|
| La pagina non si carica | Avviare `start.bat` o `mac/starten_mac.command`. Registro: `%LOCALAPPDATA%\obsidian-agent\gui.log` o `~/Library/Logs/obsidian-agent/` |
| «Cartella del vault non trovata» | Correggere il percorso nelle *Impostazioni* |
| Un asset non compare | La nota ha `typ: asset`? Si trova fuori dalla cartella dei modelli? Poi fare clic su **Aggiorna ora** |
| Un'attività non si può spuntare | La nota è stata modificata nel frattempo: fare clic su **Aggiorna ora** e riprovare |
| Orario di sincronizzazione o porta modificati | Rieseguire la configurazione (`installieren.bat` o `mac/installieren_mac.command`) |
