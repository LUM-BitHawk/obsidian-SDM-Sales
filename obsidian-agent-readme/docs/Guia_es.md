# Obsidian-Agent SDM – Guía

**Idiomas:** [Deutsch](Anleitung_de.md) · [English](Guide_en.md) · [Français](Guide_fr.md) · [Italiano](Guida_it.md) · [Español](Guia_es.md) · [Română](Ghid_ro.md)

Obsidian-Agent es una interfaz web local para la **gestión de la prestación de servicios (SDM) y las ventas**. Lee su bóveda de Obsidian y muestra en un solo lugar clientes, activos de TI con su ciclo de vida, oportunidades, reuniones y tareas. Solo se ejecuta en su ordenador, en `http://127.0.0.1:5055`.

**Principio básico:** los archivos Markdown de la bóveda son la única fuente de verdad. La base de datos `planer.db` es solo un índice y se reconstruye en cada sincronización. Puede seguir trabajando directamente en Obsidian en cualquier momento.

---

## 1. Instalación

**Requisitos:** Python 3.10 o superior (Windows) o 3.9 o superior (macOS), y conexión a Internet en el primer inicio. En ese momento el agente descarga el paquete Flask.

### Windows – `ObsidianAgent-SDM-Setup.exe`
1. Instale Python desde [python.org](https://www.python.org/downloads/windows/) y marque **«Add python.exe to PATH»**.
2. Descomprima `ObsidianAgent-SDM-Setup-Windows.zip` y ejecute `ObsidianAgent-SDM-Setup.exe`. No se necesitan derechos de administrador.
   Si aparece *«Windows protegió su PC»*, haga clic en **Más información → Ejecutar de todas formas**. El instalador no está firmado digitalmente.
3. El asistente le guía en cinco pasos:
   - Elija el **idioma**.
   - **Carpeta de instalación y opciones:** se propone `%LOCALAPPDATA%\Programs\Obsidian-Agent`. Elija si el agente se inicia automáticamente al iniciar sesión y si se crean accesos directos en el menú Inicio y en el escritorio.
   - **Bóveda de Obsidian:** elija la carpeta de su bóveda o marque «Configurarla más tarde en los ajustes».
   - **Instalar:** el instalador copia el programa y configura el entorno de Python, la bóveda y la sincronización diaria (04:00; las ejecuciones perdidas se recuperan) sin ventana de consola.
   - **Finalizar:** con «Abrir Obsidian-Agent SDM» la interfaz se abre en el navegador.
4. Más adelante, inicie el agente desde el acceso directo del escritorio, el menú Inicio **Obsidian-Agent SDM** o un marcador a `http://127.0.0.1:5055`.

### macOS – `ObsidianAgent-SDM.pkg`
1. Instale Python desde [python.org](https://www.python.org/downloads/macos/) o ejecute `brew install python` en Terminal.
2. Descomprima `ObsidianAgent-SDM-Mac.zip` y abra el paquete con **clic derecho → Abrir**. No está firmado. Si macOS lo bloquea de todos modos: *Ajustes del Sistema → Privacidad y seguridad → Abrir igualmente*.
3. El paquete instala el programa en `~/Applications/Obsidian-Agent` y abre una ventana de Terminal para la configuración. Allí elige la bóveda y decide si el agente se inicia automáticamente al iniciar sesión.
4. Si la bóveda está en *Documentos*, en el *Escritorio* o en *iCloud Drive*, macOS puede bloquear la sincronización en segundo plano. El script indica entonces qué archivo debe permitir en *Acceso total al disco*.
5. Inicio: doble clic en `~/Applications/Obsidian-Agent/mac/starten_mac.command` o un marcador a `http://127.0.0.1:5055`.

### Sin instalador (ZIP) y Linux
Descomprima el ZIP (no dentro de la bóveda) y ejecute:
- Windows: `installieren.bat`
- macOS: `mac/installieren_mac.command`
- Linux: `./start.sh`

Puede indicar el idioma como parámetro, p. ej. `installieren.bat es`. Para la sincronización diaria en Linux, añada con `crontab -e`: `0 4 * * * cd /ruta/obsidian-agent && .venv/bin/python agent.py sync --geplant`

---

## 2. La interfaz

| Página | Para qué sirve |
|---|---|
| **Hoy** | Tareas pendientes, acciones de ciclo de vida sin oportunidad, oportunidades con cierre vencido, próximas reuniones, clientes en estado rojo |
| **Clientes** | Fichas con SLA, estado, próximo caso de ciclo de vida y pipeline; un clic abre el perfil del cliente con la barra de ciclo de vida de 24 meses, activos, oportunidades, reuniones, contactos, tareas y escalados |
| **Ciclo de vida** | Todos los activos, licencias y contratos con fase y preaviso; «ninguna – ¿crear?» crea directamente una oportunidad vinculada |
| **Pipeline** | Kanban por fase con volumen total y ponderado; la fase se cambia directamente en la tarjeta |
| **Reuniones** | Reuniones periódicas, revisiones de servicio, QBR, etc.; un acta nueva incluye automáticamente los temas de ciclo de vida y las tareas abiertas del cliente |
| **Tareas** | Todas las tareas con casilla de la bóveda; se marcan directamente en la lista |
| **Búsqueda** | Búsqueda de texto completo en toda la bóveda |
| **Ajustes** | Idioma, ruta de la bóveda, nombres de carpetas, moneda, hora de sincronización, reescritura de plantillas |

El **idioma** se cambia abajo a la izquierda en la navegación o en los ajustes.

---

## 3. Estructura de la bóveda

En la primera sincronización, el agente crea estas carpetas si faltan. Los nombres siguen el idioma elegido. Las carpetas existentes nunca se renombran.

| Carpeta (español) | Contenido |
|---|---|
| `00_Cockpit` | `Hoy.md` (se reescribe en cada sincronización) y `Tareas.md` |
| `01_Clientes` | una nota por cliente |
| `02_Contactos` | personas de contacto |
| `03_Reuniones` | actas |
| `04_Servicio_Escalados` | incidencias y escalados |
| `05_Ventas_Oportunidades` | oportunidades |
| `06_Activos_TI_Ciclo_de_vida` | hardware, software, licencias, contratos, certificados |
| `99_Plantillas` | plantillas para el complemento principal «Plantillas» de Obsidian |

El agente reconoce las notas por la propiedad `typ`, no por la carpeta. Por tanto, puede archivarlas de otra forma.

### Propiedades
Los nombres de las propiedades son iguales en todos los idiomas para que equipos mixtos puedan compartir una bóveda. Los valores permitidos figuran entre paréntesis. También se reconocen variantes libres como «Verde», «Green» o «Lifecycle management».

| `typ` | Propiedades principales |
|---|---|
| `kunde` (cliente) | `sla_level` (gold, silber, bronze) · `status` (gruen, gelb, rot) · `vertragsende` (fin de contrato) · `kuendigungsfrist_monate` (preaviso en meses) · `jahresumsatz` (facturación anual) · `sdm_lead` · `account_manager` |
| `kontakt` (contacto) | `kunde` · `funktion` · `rolle` (entscheider, einfluss, anwender, einkauf) · `stimmung` (positiv, neutral, kritisch) · `loeschen_nach` (borrar después de) |
| `asset` (activo) | `kunde` · `kategorie` (hardware, netzwerk, betriebssystem, applikation, lizenz, vertrag, zertifikat) · `produkt_version` · `end_of_support` · `end_of_life` · `vertragsende` · `kuendigungsfrist_monate` · `esu_bis` · `kritikalitaet` (hoch, mittel, niedrig) · `betriebsstatus` (planung, betrieb, auslauf, ausser_betrieb) · `verknuepfte_opportunity` |
| `opportunity` | `kunde` · `phase` (qualifizierung, angebot, verhandlung, gewonnen, verloren) · `wahrscheinlichkeit` (probabilidad) · `volumen` · `abschluss_geplant` (cierre previsto) · `quelle` (lifecycle, service, bestandskunde, neukunde) · `verknuepftes_asset` |
| `meeting` (reunión) | `kunde` · `art` (jour_fixe, service_review, qbr, call, workshop) · `datum` · `teilnehmer` |
| `eskalation` (escalado) | `kunde` · `prioritaet` (p1, p2, p3) · `status` (offen, geschlossen) |

Otras reglas:
- **Formato de fecha:** `AAAA-MM-DD`.
- **Vincular un cliente:** `kunde: "[[Cliente_Ejemplo_SA]]"`. También funciona solo el nombre (`kunde: Ejemplo SA`).
- **Evite comentarios en el YAML** (`# …`): Obsidian los borra al editar las propiedades.

### Fases del ciclo de vida
El agente calcula una **fecha de acción**. Es la más temprana de estas dos fechas:
- el fin del soporte o el fin de vida
- el fin del contrato menos el preaviso

| Fase | Fecha de acción dentro de | Qué hacer ahora |
|---|---|---|
| Estrategia | ≤ 12 meses | tratarlo en la revisión de servicio, iniciar la planificación del presupuesto |
| Oferta | ≤ 6 meses | concepto con delivery, preparar la oferta |
| Ejecución | ≤ 3 meses | pedir, respetar los plazos, planificar la ventana de migración |
| Plazo vencido / Vencido | superada | aclarar de inmediato; con `esu_bis` se muestra «ESU hasta …» |

Los activos con `kritikalitaet: hoch` (alta) avanzan una fase.

### Tareas
Las tareas son líneas normales con casilla en cualquier nota, compatibles con el complemento de Obsidian **Tasks**:

```
- [ ] Enviar la oferta 📅 2026-12-01
```

Las tareas nuevas creadas desde la interfaz se añaden a la sección «✅ Tareas y próximos pasos» de la nota del cliente. Marcar una tarea escribe `[x]` y `✅ fecha`.

---

## 4. Ajustes de Obsidian recomendados
- **Complementos principales:** *Propiedades* y *Plantillas*. Configure la carpeta de plantillas como `99_Plantillas`.
- **Opcional:** el complemento de la comunidad **Tasks** para listas de tareas en Obsidian y el complemento principal **Bases** para tablas propias. Dataview ya no es necesario.

---

## 5. Actualización desde la versión anterior (versión de proyectos)
- La base de datos se reconstruye automáticamente. Su bóveda no cambia, y las carpetas antiguas (proyectos, wiki, DevOps …) se mantienen.
- Las entradas antiguas de ciclo de vida (`LCM_*.md` o `typ: lcm`) se reconocen como activos. El campo `projekt` se lee como cliente si existe un cliente con ese nombre.
- Las tareas programadas conservan su nombre y, tras la instalación, apuntan a la nueva carpeta del programa.
- Para conservar el `config.json` anterior (ruta de la bóveda, puerto), cópielo en la nueva carpeta del programa antes de la configuración.

## 6. Desinstalación y traslado
- **Windows:** *Configuración → Aplicaciones → Obsidian-Agent SDM → Desinstalar*, o `deinstallieren.bat`.
- **macOS:** `mac/deinstallieren_mac.command`.

Ambos detienen el agente, eliminan el inicio automático y la sincronización diaria, y guardan `config.json` y `planer.db` en el escritorio (`obsidian-agent-backup_<fecha>`). **La bóveda nunca se modifica.**

**Traslado:** instale en la nueva ubicación y copie `config.json` de la copia de seguridad en la carpeta del programa.

## 7. Protección de datos
Las notas de contacto contienen datos personales. Registre solo datos objetivos y profesionalmente necesarios, indique `loeschen_nach` y siga las normas de su empresa (RGPD). Una bóveda compartida no ofrece permisos detallados.

## 8. Solución de problemas

| Problema | Solución |
|---|---|
| La página no carga | Ejecute `start.bat` o `mac/starten_mac.command`. Registro: `%LOCALAPPDATA%\obsidian-agent\gui.log` o `~/Library/Logs/obsidian-agent/` |
| «Carpeta de la bóveda no encontrada» | Corrija la ruta en *Ajustes* |
| Un activo no aparece | ¿La nota tiene `typ: asset`? ¿Está fuera de la carpeta de plantillas? Después pulse **Actualizar ahora** |
| No se puede marcar una tarea | La nota ha cambiado mientras tanto: pulse **Actualizar ahora** e inténtelo de nuevo |
| Se cambió la hora de sincronización o el puerto | Vuelva a ejecutar la configuración (`installieren.bat` o `mac/installieren_mac.command`) |
