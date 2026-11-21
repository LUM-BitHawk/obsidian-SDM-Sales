# Obsidian-Agent SDM – Guide

**Langues :** [Deutsch](Anleitung_de.md) · [English](Guide_en.md) · [Français](Guide_fr.md) · [Italiano](Guida_it.md) · [Español](Guia_es.md) · [Română](Ghid_ro.md)

Obsidian-Agent est une interface web locale pour le **Service Delivery Management et la vente**. Il lit votre coffre Obsidian et réunit au même endroit les clients, les actifs IT et leur cycle de vie, les opportunités, les réunions et les tâches. Il fonctionne uniquement sur votre ordinateur, à l'adresse `http://127.0.0.1:5055`.

**Principe :** les fichiers Markdown du coffre sont la seule source de vérité. La base de données `planer.db` n'est qu'un index, reconstruit à chaque synchronisation. Vous pouvez continuer à travailler directement dans Obsidian à tout moment.

---

## 1. Installation

**Prérequis :** Python 3.10 ou plus récent (Windows) ou 3.9 ou plus récent (macOS), ainsi qu'une connexion Internet au premier démarrage. L'agent télécharge alors le paquet Flask.

### Windows – `ObsidianAgent-SDM-Setup.exe`
1. Installer Python depuis [python.org](https://www.python.org/downloads/windows/) en cochant **«Add python.exe to PATH»**.
2. Décompresser `ObsidianAgent-SDM-Setup-Windows.zip` et lancer `ObsidianAgent-SDM-Setup.exe`. Aucun droit d'administrateur n'est nécessaire.
   Si *«Windows a protégé votre ordinateur»* s'affiche, cliquer sur **Informations complémentaires → Exécuter quand même**. Le programme d'installation n'est pas signé numériquement.
3. L'assistant vous guide en cinq étapes :
   - Choisir la **langue**.
   - **Dossier d'installation et options :** le dossier proposé est `%LOCALAPPDATA%\Programs\Obsidian-Agent`. Indiquez si l'agent démarre automatiquement à l'ouverture de session et si des raccourcis sont créés dans le menu Démarrer et sur le bureau.
   - **Coffre Obsidian :** choisir le dossier de votre coffre ou cocher «Définir plus tard dans les paramètres».
   - **Installer :** le programme est copié, puis l'environnement Python, le coffre et la synchronisation quotidienne (04:00, les exécutions manquées sont rattrapées) sont configurés sans fenêtre de console.
   - **Terminer :** avec «Ouvrir Obsidian-Agent SDM», l'interface s'ouvre dans le navigateur.
4. Par la suite, démarrez l'agent via le raccourci du bureau, le menu Démarrer **Obsidian-Agent SDM** ou un favori vers `http://127.0.0.1:5055`.

### macOS – `ObsidianAgent-SDM.pkg`
1. Installer Python depuis [python.org](https://www.python.org/downloads/macos/) ou exécuter `brew install python` dans le Terminal.
2. Décompresser `ObsidianAgent-SDM-Mac.zip` puis ouvrir le paquet par **clic droit → Ouvrir**. Il n'est pas signé. Si macOS le bloque malgré tout : *Réglages Système → Confidentialité et sécurité → Ouvrir quand même*.
3. Le paquet installe le logiciel dans `~/Applications/Obsidian-Agent` et ouvre une fenêtre Terminal pour la configuration. Vous y choisissez le coffre et décidez si l'agent démarre automatiquement à la connexion.
4. Si le coffre se trouve dans *Documents*, sur le *Bureau* ou dans *iCloud Drive*, macOS peut bloquer la synchronisation en arrière-plan. Le script indique alors quel fichier autoriser sous *Accès complet au disque*.
5. Démarrage : double-clic sur `~/Applications/Obsidian-Agent/mac/starten_mac.command` ou favori vers `http://127.0.0.1:5055`.

### Sans programme d'installation (ZIP) et Linux
Décompresser le ZIP (pas dans le coffre) puis lancer :
- Windows : `installieren.bat`
- macOS : `mac/installieren_mac.command`
- Linux : `./start.sh`

La langue peut être passée en paramètre, p. ex. `installieren.bat fr`. Pour la synchronisation quotidienne sous Linux, ajouter via `crontab -e` : `0 4 * * * cd /chemin/obsidian-agent && .venv/bin/python agent.py sync --geplant`

---

## 2. L'interface

| Page | Usage |
|---|---|
| **Aujourd'hui** | Tâches à échéance, actions de cycle de vie sans opportunité, opportunités à clôture dépassée, prochaines réunions, clients en statut rouge |
| **Clients** | Tuiles avec SLA, statut, prochain cas de cycle de vie et pipeline ; un clic ouvre la fiche client avec la barre de cycle de vie sur 24 mois, les actifs, opportunités, réunions, contacts, tâches et escalades |
| **Cycle de vie** | Tous les actifs, licences et contrats avec phase et préavis ; «aucune – créer ?» crée directement une opportunité liée |
| **Pipeline** | Kanban par phase avec volume total et pondéré ; la phase se modifie directement sur la carte |
| **Réunions** | Points fixes, revues de service, QBR, etc. ; un nouveau procès-verbal reprend automatiquement les sujets de cycle de vie et les tâches ouvertes du client |
| **Tâches** | Toutes les tâches à cocher du coffre ; cocher directement dans la liste |
| **Recherche** | Recherche plein texte dans tout le coffre |
| **Paramètres** | Langue, chemin du coffre, noms des dossiers, devise, heure de synchronisation, réécriture des modèles |

La **langue** se change en bas à gauche de la navigation ou dans les paramètres.

---

## 3. Organisation du coffre

Lors de la première synchronisation, l'agent crée ces dossiers s'ils manquent. Leurs noms suivent la langue choisie. Les dossiers existants ne sont jamais renommés.

| Dossier (français) | Contenu |
|---|---|
| `00_Cockpit` | `Aujourd'hui.md` (réécrit à chaque synchronisation) et `Tâches.md` |
| `01_Clients` | une note par client |
| `02_Contacts` | interlocuteurs |
| `03_Reunions` | procès-verbaux |
| `04_Service_Escalades` | incidents et escalades |
| `05_Ventes_Opportunites` | opportunités |
| `06_Actifs_IT_Cycle_de_vie` | matériel, logiciels, licences, contrats, certificats |
| `99_Modeles` | modèles pour le plugin principal «Modèles» d'Obsidian |

L'agent reconnaît les notes à la propriété `typ`, pas au dossier. Vous pouvez donc les classer autrement.

### Propriétés
Les noms des propriétés sont identiques dans toutes les langues, afin que des équipes mixtes puissent partager un coffre. Les valeurs autorisées figurent entre parenthèses. Les variantes libres comme «Vert», «Green» ou «Lifecycle management» sont aussi reconnues.

| `typ` | Propriétés principales |
|---|---|
| `kunde` (client) | `sla_level` (gold, silber, bronze) · `status` (gruen, gelb, rot) · `vertragsende` (fin de contrat) · `kuendigungsfrist_monate` (préavis en mois) · `jahresumsatz` (chiffre d'affaires) · `sdm_lead` · `account_manager` |
| `kontakt` (contact) | `kunde` · `funktion` · `rolle` (entscheider, einfluss, anwender, einkauf) · `stimmung` (positiv, neutral, kritisch) · `loeschen_nach` (supprimer après) |
| `asset` (actif) | `kunde` · `kategorie` (hardware, netzwerk, betriebssystem, applikation, lizenz, vertrag, zertifikat) · `produkt_version` · `end_of_support` · `end_of_life` · `vertragsende` · `kuendigungsfrist_monate` · `esu_bis` · `kritikalitaet` (hoch, mittel, niedrig) · `betriebsstatus` (planung, betrieb, auslauf, ausser_betrieb) · `verknuepfte_opportunity` |
| `opportunity` | `kunde` · `phase` (qualifizierung, angebot, verhandlung, gewonnen, verloren) · `wahrscheinlichkeit` (probabilité) · `volumen` · `abschluss_geplant` (clôture prévue) · `quelle` (lifecycle, service, bestandskunde, neukunde) · `verknuepftes_asset` |
| `meeting` (réunion) | `kunde` · `art` (jour_fixe, service_review, qbr, call, workshop) · `datum` · `teilnehmer` |
| `eskalation` (escalade) | `kunde` · `prioritaet` (p1, p2, p3) · `status` (offen, geschlossen) |

Autres règles :
- **Format de date :** `AAAA-MM-JJ`.
- **Lier un client :** `kunde: "[[Client_Exemple_SA]]"`. Le nom seul (`kunde: Exemple SA`) fonctionne aussi.
- **Éviter les commentaires dans le YAML** (`# …`) : Obsidian les supprime lors de la modification des propriétés.

### Phases du cycle de vie
L'agent calcule une **date d'action**. C'est la plus proche des deux dates suivantes :
- la fin du support ou la fin de vie
- la fin du contrat moins le préavis

| Phase | Date d'action dans | Que faire maintenant |
|---|---|---|
| Stratégie | ≤ 12 mois | aborder le sujet en revue de service, lancer la planification budgétaire |
| Offre | ≤ 6 mois | concept avec la delivery, établir l'offre |
| Mise en œuvre | ≤ 3 mois | commander, respecter les délais, planifier la fenêtre de migration |
| Délai manqué / Expiré | dépassée | clarifier immédiatement ; avec `esu_bis`, «ESU jusqu'au …» s'affiche |

Les actifs avec `kritikalitaet: hoch` (élevée) avancent d'une phase.

### Tâches
Les tâches sont de simples lignes à cocher dans n'importe quelle note, compatibles avec le plugin Obsidian **Tasks** :

```
- [ ] Envoyer l'offre 📅 2026-12-01
```

Les nouvelles tâches créées dans l'interface sont ajoutées à la section «✅ Tâches & prochaines étapes» de la note client. Cocher une tâche écrit `[x]` et `✅ date`.

---

## 4. Réglages Obsidian recommandés
- **Plugins principaux :** *Propriétés* et *Modèles*. Définir le dossier des modèles sur `99_Modeles`.
- **Facultatif :** le plugin communautaire **Tasks** pour les listes de tâches dans Obsidian et le plugin principal **Bases** pour vos propres tableaux. Dataview n'est plus nécessaire.

---

## 5. Mise à jour depuis la version précédente (version projets)
- La base de données est reconstruite automatiquement. Votre coffre reste inchangé, et les anciens dossiers (projets, wiki, DevOps …) restent en place.
- Les anciennes entrées de cycle de vie (`LCM_*.md` ou `typ: lcm`) sont reconnues comme actifs. Le champ `projekt` est lu comme client lorsqu'un client portant ce nom existe.
- Les tâches planifiées conservent leur nom et pointent après l'installation vers le nouveau dossier du programme.
- Pour reprendre l'ancien `config.json` (chemin du coffre, port), copiez-le dans le nouveau dossier du programme avant la configuration.

## 6. Désinstallation et déménagement
- **Windows :** *Paramètres → Applications → Obsidian-Agent SDM → Désinstaller*, ou `deinstallieren.bat`.
- **macOS :** `mac/deinstallieren_mac.command`.

Les deux arrêtent l'agent, suppriment le démarrage automatique et la synchronisation quotidienne, et sauvegardent `config.json` et `planer.db` sur le bureau (`obsidian-agent-backup_<date>`). **Le coffre n'est jamais modifié.**

**Déménagement :** installer au nouvel emplacement et copier `config.json` depuis la sauvegarde dans le dossier du programme.

## 7. Protection des données
Les notes de contact contiennent des données personnelles. Saisissez uniquement des informations factuelles et professionnellement nécessaires, renseignez `loeschen_nach` et respectez les règles de votre employeur (RGPD / nLPD). Un coffre partagé n'offre pas de droits d'accès fins.

## 8. Dépannage

| Problème | Solution |
|---|---|
| La page ne se charge pas | Lancer `start.bat` ou `mac/starten_mac.command`. Journal : `%LOCALAPPDATA%\obsidian-agent\gui.log` ou `~/Library/Logs/obsidian-agent/` |
| «Dossier du coffre introuvable» | Corriger le chemin dans les *Paramètres* |
| Un actif n'apparaît pas | La note contient-elle `typ: asset` ? Se trouve-t-elle hors du dossier des modèles ? Puis cliquer sur **Actualiser** |
| Une tâche ne peut pas être cochée | La note a été modifiée entre-temps : cliquer sur **Actualiser** et réessayer |
| Heure de synchronisation ou port modifiés | Relancer la configuration (`installieren.bat` ou `mac/installieren_mac.command`) |
