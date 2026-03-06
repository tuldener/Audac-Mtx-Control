# Audac MTX

[![HACS Custom](https://img.shields.io/badge/HACS-Custom-41BDF5.svg?style=flat-square)](https://github.com/hacs/integration)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=flat-square)](https://opensource.org/licenses/MIT)
[![Version](https://img.shields.io/badge/Version-1.9.2-green.svg?style=flat-square)](https://github.com/tuldener/Audac-Mtx-Control)

Home Assistant Integration zur Steuerung von **Audac MTX** Audio-Matrizen (MTX48 / MTX88).

Kommuniziert direkt per TCP mit dem MTX-Gerät und liefert eine Bubble Card-inspirierte Lovelace Card mit.

---

## Features

- **Direkte TCP-Verbindung** – Kommuniziert direkt mit dem Audac MTX (Port 5001)
- **Media Player Entities** – Jede Zone wird als eigener Media Player dargestellt
- **Zonensteuerung** – Lautstärke, Mute, Quellenauswahl pro Zone
- **Bass & Höhen** – Anzeige und Steuerung der Klangregelung (±14 dB)
- **Quellenauswahl** – Übersichtliches Grid mit allen verfügbaren Eingängen
- **Automatische Erkennung** – Die Card findet alle MTX-Zonen automatisch
- **Benutzerdefinierte Namen** – Zonen und Quellen individuell benennen (über Optionen)
- **Quellen-Sichtbarkeit** – Einzelne Quellen ausblenden (z.B. nicht belegte Eingänge)
- **Dark / Light Mode** – Automatisch oder manuell wählbar
- **Akzentfarbe** – Frei wählbare Akzentfarbe im Card-Editor
- **Akkordeon-Navigation** – Nur eine Zone gleichzeitig geöffnet
- **Bubble Card Design** – Abgerundete Ecken, sanfte Gradienten, flüssige Animationen
- **Card Editor** – Visuelle Konfiguration direkt im Lovelace-Editor
- **Auto-Reconnect** – Exponentieller Backoff bei Verbindungsabbruch (max. 30 s)
- **Stabiler Coordinator** – asyncio.Lock-Reset verhindert Deadlocks bei Timeouts

---

## Voraussetzungen

- Home Assistant 2023.9.0 oder neuer
- [HACS](https://hacs.xyz/) (empfohlen)
- Audac MTX48 oder MTX88, erreichbar im Netzwerk (TCP Port 5001)

---

## Installation

### Über HACS (empfohlen)

1. Öffne HACS in Home Assistant
2. Gehe zu **Integrationen** → drei Punkte → **Benutzerdefinierte Repositories**
3. Füge `https://github.com/tuldener/Audac-Mtx-Control` hinzu, Kategorie **Integration**
4. Suche nach **Audac MTX** und installiere es
5. Starte Home Assistant neu
6. Gehe zu **Einstellungen** → **Geräte & Dienste** → **Integration hinzufügen** → **Audac MTX**

### Manuell

1. Kopiere den Ordner `custom_components/audac_mtx/` nach `config/custom_components/audac_mtx/`
2. Starte Home Assistant neu
3. Gehe zu **Einstellungen** → **Geräte & Dienste** → **Integration hinzufügen** → **Audac MTX**

---

## Einrichtung

### Integration konfigurieren

Beim Hinzufügen der Integration werden folgende Daten abgefragt:

| Feld | Beschreibung | Standard |
|------|-------------|----------|
| Host / IP-Adresse | IP des MTX-Geräts | – |
| Port | TCP-Port | `5001` |
| Modell | MTX48 (4 Zonen) oder MTX88 (8 Zonen) | `MTX88` |
| Gerätename | Anzeigename in Home Assistant | `Audac MTX` |

### Zonen- und Quellennamen anpassen

Über **Einstellungen** → **Geräte & Dienste** → **Audac MTX** → **Konfigurieren** können individuelle Namen und Sichtbarkeit vergeben werden:

- **Zonennamen** (z.B. „Empfangsbereich", „Konferenzraum", „Terrasse")
- **Zonensichtbarkeit** – einzelne Zonen ausblenden
- **Quellennamen** (z.B. „Spotify", „Radio", „Mikrofon Bühne")
- **Quellensichtbarkeit** – nicht belegte Eingänge ausblenden

> **Hinweis:** Die Quelle „Off" (Routing = 0) ist standardmäßig ausgeblendet. Sie kann in den Optionen sichtbar geschaltet werden, um eine Zone über die Quellenauswahl abschalten zu können.

---

## Lovelace Card

Die Card wird automatisch als Lovelace-Ressource registriert. Falls nicht, manuell hinzufügen:

```yaml
resources:
  - url: /audac_mtx/audac-mtx-card.js
    type: module
```

### Einfach (Automatische Erkennung)

```yaml
type: custom:audac-mtx-card
title: Audac MTX
```

### Manuell (Zonen einzeln konfigurieren)

```yaml
type: custom:audac-mtx-card
title: Audio Steuerung
zones:
  - entity: media_player.audac_mtx_zone_1
    name: Empfangsbereich
  - entity: media_player.audac_mtx_zone_2
    name: Konferenzraum
  - entity: media_player.audac_mtx_zone_3
    name: Restaurant
show_bass_treble: true
show_source: true
theme: auto
accent_color: "#7c6bf0"
```

### Card-Optionen

| Option | Beschreibung | Standard |
|--------|-------------|----------|
| `title` | Titel der Karte | `Audac MTX` |
| `zones` | Liste der Zonen (leer = Auto-Erkennung) | `[]` |
| `show_source` | Quellenauswahl anzeigen | `true` |
| `show_bass_treble` | Bass/Höhen anzeigen | `true` |
| `theme` | Design: `auto`, `dark`, `light` | `auto` |
| `accent_color` | Akzentfarbe als Hex (leer = Standard lila) | `""` |

### Zone-Konfiguration

| Option | Beschreibung |
|--------|-------------|
| `entity` | Entity-ID des Media Players (z.B. `media_player.audac_mtx_zone_1`) |
| `name` | Anzeigename (optional, sonst wird `friendly_name` verwendet) |

### Weitere Card-Typen

Neben der Haupt-Card gibt es spezialisierte Einzel-Cards:

| Card-Typ | Beschreibung |
|----------|-------------|
| `custom:audac-mtx-volume-card` | Lautstärke-Regler für eine einzelne Zone |
| `custom:audac-mtx-source-card` | Quellenauswahl für eine einzelne Zone |
| `custom:audac-mtx-bass-card` | Bass-Regler für eine einzelne Zone |
| `custom:audac-mtx-treble-card` | Höhen-Regler für eine einzelne Zone |

---

## Entities

Pro Zone werden folgende Entities erstellt:

| Entity-Typ | Kategorie | Beschreibung |
|------------|-----------|-------------|
| `media_player` | – | Haupt-Entity mit Lautstärke, Mute, Quelle |
| `select` | CONFIG | Quellenauswahl |
| `number` | CONFIG | Lautstärke 0–100 % |
| `switch` | CONFIG | Mute Ein/Aus |
| `sensor` | DIAGNOSTIC | Aktive Quelle (Lesezugriff) |

### Eingänge (Input-Nummern)

| ID | Bezeichnung | Typ |
|----|-------------|-----|
| 0 | Off | – |
| 1 | Mic 1 | Balanced XLR (Phantomspeisung 15 V, Priorität) |
| 2 | Mic 2 | Balanced XLR (Phantomspeisung 15 V, Priorität) |
| 3 | Line 3 | Unbalanced Stereo RCA |
| 4 | Line 4 | Unbalanced Stereo RCA |
| 5 | Line 5 | Unbalanced Stereo RCA |
| 6 | Line 6 | Unbalanced Stereo RCA |
| 7 | Wall Panel (WLI/MWX65) | RJ45 Wandeingang |
| 8 | Wall Panel (WMI) | RJ45 Wandeingang |

> **MTX48:** Nutzt Eingänge 1–6 + Wandeingänge 7/8. Eingänge 5 und 6 sind physisch nicht vorhanden (4 Line-Eingänge), können aber in den Optionen ausgeblendet werden.

### Media Player Attribute

| Attribut | Beschreibung |
|----------|-------------|
| `volume_level` | Lautstärke (0.0 – 1.0) |
| `is_volume_muted` | Stummschaltung |
| `source` | Aktive Quelle |
| `source_list` | Verfügbare Quellen |
| `bass` | Bass-Einstellung (dB, -14 bis +14) |
| `treble` | Höhen-Einstellung (dB, -14 bis +14) |
| `volume_db` | Lautstärke in dB (0 bis -70) |
| `routing` | Aktive Routing-ID (0=Off, 1-8=Eingänge) |

---

## Custom Services

| Service | Parameter | Beschreibung |
|---------|-----------|-------------|
| `media_player.set_bass` | `bass` (0–14) | Bass setzen (7 = 0 dB / neutral) |
| `media_player.set_treble` | `treble` (0–14) | Höhen setzen (7 = 0 dB / neutral) |
| `media_player.routing_up` | – | Zum nächsten Eingang wechseln (`SRU0x`) |
| `media_player.routing_down` | – | Zum vorherigen Eingang wechseln (`SRD0x`) |

Beispiele in einer Automation:

```yaml
# Bass auf +4 dB setzen
service: media_player.set_bass
target:
  entity_id: media_player.audac_mtx_zone_1
data:
  bass: 9

# Nächste Quelle wählen
service: media_player.routing_up
target:
  entity_id: media_player.audac_mtx_zone_2
```

---

## MTX-Protokoll

### Verbindung

| Port | Protokoll | Parameter |
|------|-----------|-----------|
| 5001 | TCP/IP | max. **1 gleichzeitige Verbindung** |
| RS232 | Seriell | 19200 Baud, 8N1 (8 Datenbits, kein Parity, 1 Stopbit) |
| RS485 | Seriell | gleiche Parameter wie RS232 |

> ⚠️ **Wichtig:** Das MTX unterstützt nur **eine** gleichzeitige TCP/IP-Verbindung. Verbindungen der Audac Touch™ App oder des Webinterfaces werden getrennt, sobald Home Assistant verbindet.

### Protokollformat

```
Befehl:  #|X001|web|CMD|ARG|U|\r\n
Antwort: #|web|X001|CMD|DATA|CRC|\r\n
Update:  #|ALL|X001|CMD|DATA|CRC|\r\n   (Broadcast nach SET)
```

- Zieladresse des MTX ist immer `X001`
- Prüfsumme: CRC-16 (kann durch `U` ersetzt werden)
- GET-Antworten entfernen den `G`-Prefix: `GVALL` → `VALL`, `GZI01` → `ZI01`

### Befehlsübersicht (vollständig)

| Befehl | Argument | Funktion |
|--------|----------|----------|
| `GZI0x` | – | Zone-Info: Volume, Routing, Mute, Bass, Treble (1 Abfrage) |
| `GVALL` | – | Lautstärke aller Zonen (Bulk) |
| `GRALL` | – | Routing aller Zonen (Bulk) |
| `GMALL` | – | Mute-Status aller Zonen (Bulk) |
| `GV0x` | – | Lautstärke einer Zone |
| `GR0x` | – | Routing einer Zone |
| `GM0x` | – | Mute-Status einer Zone |
| `GB0x` | – | Bass einer Zone |
| `GT0x` | – | Höhen einer Zone |
| `SVx` | 0–70 | Lautstärke setzen (0=max, 70=min = -70 dB) |
| `SVU0x` | 0 | Lautstärke +3 dB |
| `SVD0x` | 0 | Lautstärke -3 dB |
| `SRx` | 0–8 | Routing/Quelle setzen (0=Off, 1–8=Eingänge) |
| `SRU0x` | 0 | Zum nächsten Eingang wechseln (deaktivierte überspringen) |
| `SRD0x` | 0 | Zum vorherigen Eingang wechseln (deaktivierte überspringen) |
| `SM0x` | 0/1 | Mute setzen (0=aus, 1=ein) |
| `SB0x` | 0–14 | Bass setzen (7 = 0 dB neutral) |
| `ST0x` | 0–14 | Treble setzen (7 = 0 dB neutral) |
| `GSV` | – | Firmware-Version abrufen |
| `SAVE` | – | Zoneneinstellungen speichern (gehen sonst beim Ausschalten verloren!) |
| `DEF` | – | ⚠️ Werksreset – alle Zonen- und Geräteeinstellungen zurücksetzen |

Bulk-Befehle werden bevorzugt verwendet; bei Nicht-Unterstützung wird automatisch auf `GZI0x` pro Zone zurückgefallen.

---

## Troubleshooting

**Integration kann keine Verbindung herstellen**
- Prüfe, ob das MTX-Gerät per Ping erreichbar ist
- Prüfe, ob Port 5001 nicht durch eine Firewall blockiert wird
- Das MTX unterstützt nur eine aktive TCP-Verbindung gleichzeitig – andere Clients (z.B. Audac-App) trennen

**Entities zeigen veraltete Werte**
- Der Polling-Intervall beträgt 60 Sekunden
- Bei Verbindungsabbruch werden die letzten bekannten Werte beibehalten
- Im HA-Log nach `audac_mtx`-Warnungen suchen

**Lovelace Card wird nicht geladen**
- Manuell unter **Einstellungen** → **Dashboards** → **Ressourcen** prüfen, ob `/audac_mtx/audac-mtx-card.js` eingetragen ist
- Browser-Cache leeren (Hard Reload / Strg+Shift+R)
- Falls `browser_mod.js` mit 404-Fehler erscheint: diesen Ressourcen-Eintrag manuell entfernen

---

## Changelog

### 1.9.2
- **Fix:** `asyncio.Lock`-Reset nach `COMMAND_TIMEOUT` – verhindert Deadlock bei Python < 3.12
- **Verbesserung:** `SCAN_INTERVAL` auf 60 s erhöht (MTX braucht ~10–45 s für alle Zonenabfragen)
- **Verbesserung:** Vereinfachte Timeout-Behandlung im Coordinator

### 1.9.1
- **Fix:** Coordinator stoppte nach erstem Scan – Backoff trat ein weil Lock während Startup gehalten war
- **Fix:** `COMMAND_TIMEOUT` von 8 s → 25 s erhöht
- **Fix:** `SCAN_INTERVAL` von 15 s → 30 s erhöht (reduziert Lock-Contention)
- **Fix:** `last_update_success = True` nach erfolgreichem Fetch – setzt Backoff zurück

### 1.9.0
- **Fix:** `asyncio.Lock`-Deadlock im Coordinator behoben – `COMMAND_TIMEOUT` und `GET_ALL_ZONES_TIMEOUT` eingeführt
- **Fix:** `UPDATE_TIMEOUT`-Guard verhindert verbleibende Freeze-Szenarien

### 1.8.0
- **Fix:** Lovelace-Ressourcen-Registrierung für HA 2024+ verbessert
- **Fix:** `add_extra_js_url` ersetzt durch robustere Lovelace-Resource-Registration
- **Fix:** `homeassistant_started`-Event als Fallback für späte Ressourcen-Registrierung

### 1.7.4
- **Fix:** Card wurde beim ersten Laden nicht angezeigt (mehrfacher Reload nötig)
- **Neu:** `ll-rebuild`-Event nach Elementregistrierung – Lovelace rendert automatisch neu

### 1.7.3
- **Fix:** `window.customCards` an den Anfang der Datei verschoben (HA liest es synchron)

### 1.7.2
- **Fix:** Sporadischer „Konfigurationsfehler" beim Reload – verschachtelte Template-Literals entfernt
- **Neu:** `connectedCallback()` ergänzt für zuverlässiges Re-Render

### 1.7.1
- **Verbesserung:** Lautstärke-Hintergrundfarbe kräftiger (38% Opacity dunkel, 26% hell)
- **Neu:** Akkordeon-Verhalten – nur eine Zone gleichzeitig offen

### 1.7.0
- **Verbesserung:** Zonennamen automatisch gekürzt („Audac MTX Bar" → „Bar")
- **Verbesserung:** Untertitel zeigt nur noch Quelle, nicht mehr Lautstärke in %
- **Verbesserung:** Prozent-Badge rechts entfernt – Lautstärke als Hintergrundfüllung
- **Fix:** `_updateExisting()` mit try/catch – bei Fehler automatischer Rebuild

### 1.6.1
- **Fix:** Zonen nach Reload nicht mehr sichtbar (Timing-Bug)
- **Fix:** „Custom element not found" nach Seitenreload
- **Verbesserung:** HACS-Updates aktualisieren Lovelace-Ressource automatisch

### 1.6.0
- **Fix:** Dauerhaftes Flackern der Card behoben – intelligentes DOM-Patching statt Rebuild
- **Neu:** Bass- und Höhen-Slider direkt in der Haupt-Card bedienbar

### 1.5.0
- **Fix:** Flackern beim Aufklappen einer Zone
- **Neu:** Bass/Höhen-Slider in Haupt-Card interaktiv

### 1.4.0
- **Neu:** Sofortige Entitäts-Aktualisierung beim Aufklappen einer Zone
- **Neu:** Akzentfarbe frei wählbar im Card-Editor

### 1.3.1
- **Verbesserung:** Polling-Intervall auf 15 Sekunden erhöht

### 1.3.0
- **Neu:** Services `routing_up` / `routing_down` (Befehle `SRU0x` / `SRD0x`)
- **Neu:** Befehl `DEF` (Werksreset) hinzugefügt
- **Fix:** Eingänge 7/8 korrekt als Wandeingänge bezeichnet
- **Verbesserung:** Protokolldokumentation vervollständigt

### 1.2.0
- **Fix:** XSS-Schutz in der Lovelace Card
- **Neu:** Debounce für Lautstärke-Slider (250 ms)
- **Neu:** Exponentieller Backoff für Reconnect-Versuche (max. 30 s)

### 1.1.0
- Modell-Auswahl (MTX48 / MTX88) im Setup-Dialog
- Bulk-Abfragen (GVALL, GRALL, GMALL) mit automatischem Fallback
- Config-Migration v1 → v2

### 1.0.0
- Erstveröffentlichung

---

## Lizenz

MIT License – siehe [LICENSE](LICENSE)
