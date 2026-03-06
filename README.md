# Audac MTX

[![HACS Custom](https://img.shields.io/badge/HACS-Custom-41BDF5.svg?style=flat-square)](https://github.com/hacs/integration)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=flat-square)](https://opensource.org/licenses/MIT)
[![Version](https://img.shields.io/badge/Version-1.2.0-green.svg?style=flat-square)](https://github.com/tuldener/Audac-Mtx-Control)

Home Assistant Integration zur Steuerung von **Audac MTX** Audio-Matrizen (MTX48 / MTX88).

Kommuniziert direkt per TCP mit dem MTX-GerÃ¤t und liefert eine Bubble Card-inspirierte Lovelace Card mit.

---

## Features

- **Direkte TCP-Verbindung** â Kommuniziert direkt mit dem Audac MTX (Port 5001)
- **Media Player Entities** â Jede Zone wird als eigener Media Player dargestellt
- **Zonensteuerung** â LautstÃ¤rke, Mute, Quellenauswahl pro Zone
- **Bass & HÃ¶hen** â Anzeige und Steuerung der Klangregelung (Â±14 dB)
- **Quellenauswahl** â Ãbersichtliches Grid mit allen verfÃ¼gbaren EingÃ¤ngen
- **Automatische Erkennung** â Die Card findet alle MTX-Zonen automatisch
- **Benutzerdefinierte Namen** â Zonen und Quellen individuell benennen (Ã¼ber Optionen)
- **Quellen-Sichtbarkeit** â Einzelne Quellen ausblenden (z.B. nicht belegte EingÃ¤nge)
- **Dark / Light Mode** â Automatisch oder manuell wÃ¤hlbar
- **Bubble Card Design** â Abgerundete Ecken, sanfte Gradienten, flÃ¼ssige Animationen
- **Card Editor** â Visuelle Konfiguration direkt im Lovelace-Editor
- **Auto-Reconnect** â Exponentieller Backoff bei Verbindungsabbruch (max. 30 s)

---

## Voraussetzungen

- Home Assistant 2023.9.0 oder neuer
- [HACS](https://hacs.xyz/) (empfohlen)
- Audac MTX48 oder MTX88, erreichbar im Netzwerk (TCP Port 5001)

---

## Installation

### Ãber HACS (empfohlen)

1. Ãffne HACS in Home Assistant
2. Gehe zu **Integrationen** â drei Punkte â **Benutzerdefinierte Repositories**
3. FÃ¼ge `https://github.com/tuldener/Audac-Mtx-Control` hinzu, Kategorie **Integration**
4. Suche nach **Audac MTX** und installiere es
5. Starte Home Assistant neu
6. Gehe zu **Einstellungen** â **GerÃ¤te & Dienste** â **Integration hinzufÃ¼gen** â **Audac MTX**

### Manuell

1. Kopiere den Ordner `custom_components/audac_mtx/` nach `config/custom_components/audac_mtx/`
2. Starte Home Assistant neu
3. Gehe zu **Einstellungen** â **GerÃ¤te & Dienste** â **Integration hinzufÃ¼gen** â **Audac MTX**

---

## Einrichtung

### Integration konfigurieren

Beim HinzufÃ¼gen der Integration werden folgende Daten abgefragt:

| Feld | Beschreibung | Standard |
|------|-------------|----------|
| Host / IP-Adresse | IP des MTX-GerÃ¤ts | â |
| Port | TCP-Port | `5001` |
| Modell | MTX48 (4 Zonen) oder MTX88 (8 Zonen) | `MTX88` |
| GerÃ¤tename | Anzeigename in Home Assistant | `Audac MTX` |

### Zonen- und Quellennamen anpassen

Ãber **Einstellungen** â **GerÃ¤te & Dienste** â **Audac MTX** â **Konfigurieren** kÃ¶nnen individuelle Namen und Sichtbarkeit vergeben werden:

- **Zonennamen** (z.B. "Empfangsbereich", "Konferenzraum", "Terrasse")
- **ZonenvisibilitÃ¤t** â einzelne Zonen ausblenden
- **Quellennamen** (z.B. "Spotify", "Radio", "Mikrofon BÃ¼hne")
- **QuellenvisibilitÃ¤t** â nicht belegte EingÃ¤nge ausblenden

> **Hinweis:** Die Quelle âOff" (Routing = 0) ist standardmÃ¤Ãig ausgeblendet. Sie kann in den Optionen sichtbar geschaltet werden, um eine Zone Ã¼ber die Quellenauswahl abschalten zu kÃ¶nnen.

---

## Lovelace Card

Die Card wird automatisch als Lovelace-Ressource registriert. Falls nicht, manuell hinzufÃ¼gen:

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
```

### Card-Optionen

| Option | Beschreibung | Standard |
|--------|-------------|----------|
| `title` | Titel der Karte | `Audac MTX` |
| `zones` | Liste der Zonen (leer = Auto-Erkennung) | `[]` |
| `show_source` | Quellenauswahl anzeigen | `true` |
| `show_bass_treble` | Bass/HÃ¶hen anzeigen | `true` |
| `theme` | Design: `auto`, `dark`, `light` | `auto` |

### Zone-Konfiguration

| Option | Beschreibung |
|--------|-------------|
| `entity` | Entity-ID des Media Players (z.B. `media_player.audac_mtx_zone_1`) |
| `name` | Anzeigename (optional, sonst wird `friendly_name` verwendet) |

### Weitere Card-Typen

Neben der Haupt-Card gibt es spezialisierte Einzel-Cards:

| Card-Typ | Beschreibung |
|----------|-------------|
| `custom:audac-mtx-volume-card` | LautstÃ¤rke-Regler fÃ¼r eine einzelne Zone |
| `custom:audac-mtx-source-card` | Quellenauswahl fÃ¼r eine einzelne Zone |
| `custom:audac-mtx-bass-card` | Bass-Regler fÃ¼r eine einzelne Zone |
| `custom:audac-mtx-treble-card` | HÃ¶hen-Regler fÃ¼r eine einzelne Zone |

---

## Entities

Pro Zone werden folgende Entities erstellt:

| Entity-Typ | Kategorie | Beschreibung |
|------------|-----------|-------------|
| `media_player` | â | Haupt-Entity mit LautstÃ¤rke, Mute, Quelle |
| `select` | CONFIG | Quellenauswahl |
| `number` | CONFIG | LautstÃ¤rke 0â100 % |
| `switch` | CONFIG | Mute Ein/Aus |
| `sensor` | DIAGNOSTIC | Aktive Quelle (Lesezugriff) |

### EingÃ¤nge (Input-Nummern)

| ID | Bezeichnung | Typ |
|----|-------------|-----|
| 0 | Off | â |
| 1 | Mic 1 | Balanced XLR (Phantomspeisung 15 V, PrioritÃ¤t) |
| 2 | Mic 2 | Balanced XLR (Phantomspeisung 15 V, PrioritÃ¤t) |
| 3 | Line 3 | Unbalanced Stereo RCA |
| 4 | Line 4 | Unbalanced Stereo RCA |
| 5 | Line 5 | Unbalanced Stereo RCA |
| 6 | Line 6 | Unbalanced Stereo RCA |
| 7 | Wall Panel (WLI/MWX65) | RJ45 Wandeingang |
| 8 | Wall Panel (WMI) | RJ45 Wandeingang |

> **MTX48:** Nutzt EingÃ¤nge 1â6 + WandeingÃ¤nge 7/8. EingÃ¤nge 5 und 6 sind physisch nicht vorhanden (4 Line-EingÃ¤nge), kÃ¶nnen aber in den Optionen ausgeblendet werden.

### Media Player Attribute

| Attribut | Beschreibung |
|----------|-------------|
| `volume_level` | LautstÃ¤rke (0.0 â 1.0) |
| `is_volume_muted` | Stummschaltung |
| `source` | Aktive Quelle |
| `source_list` | VerfÃ¼gbare Quellen |
| `bass` | Bass-Einstellung (dB, -14 bis +14) |
| `treble` | HÃ¶hen-Einstellung (dB, -14 bis +14) |
| `volume_db` | LautstÃ¤rke in dB (0 bis -70) |
| `routing` | Aktive Routing-ID (0=Off, 1-8=EingÃ¤nge) |

---

## Custom Services

| Service | Parameter | Beschreibung |
|---------|-----------|-------------|
| `media_player.set_bass` | `bass` (0â14) | Bass setzen (7 = 0 dB / neutral) |
| `media_player.set_treble` | `treble` (0â14) | HÃ¶hen setzen (7 = 0 dB / neutral) |
| `media_player.routing_up` | â | Zum nÃ¤chsten Eingang wechseln (`SRU0x`) |
| `media_player.routing_down` | â | Zum vorherigen Eingang wechseln (`SRD0x`) |

Beispiele in einer Automation:

```yaml
# Bass auf +4 dB setzen
service: media_player.set_bass
target:
  entity_id: media_player.audac_mtx_zone_1
data:
  bass: 9

# NÃ¤chste Quelle wÃ¤hlen
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

> â ï¸ **Wichtig:** Das MTX unterstÃ¼tzt nur **eine** gleichzeitige TCP/IP-Verbindung. Verbindungen der Audac Touchâ¢ App oder des Webinterfaces werden getrennt, sobald Home Assistant verbindet.

### Protokollformat

```
Befehl:  #|X001|web|CMD|ARG|U|\r\n
Antwort: #|web|X001|CMD|DATA|CRC|\r\n
Update:  #|ALL|X001|CMD|DATA|CRC|\r\n   (Broadcast nach SET)
```

- Zieladresse des MTX ist immer `X001`
- PrÃ¼fsumme: CRC-16 (kann durch `U` ersetzt werden)
- GET-Antworten entfernen den `G`-Prefix: `GVALL` â `VALL`, `GZI01` â `ZI01`

### BefehlsÃ¼bersicht (vollstÃ¤ndig)

| Befehl | Argument | Funktion |
|--------|----------|---------|
| `GZI0x` | â | Zone-Info: Volume, Routing, Mute, Bass, Treble (1 Abfrage) |
| `GVALL` | â | LautstÃ¤rke aller Zonen (Bulk) |
| `GRALL` | â | Routing aller Zonen (Bulk) |
| `GMALL` | â | Mute-Status aller Zonen (Bulk) |
| `GV0x` | â | LautstÃ¤rke einer Zone |
| `GR0x` | â | Routing einer Zone |
| `GM0x` | â | Mute-Status einer Zone |
| `GB0x` | â | Bass einer Zone |
| `GT0x` | â | HÃ¶hen einer Zone |
| `SVx` | 0â70 | LautstÃ¤rke setzen (0=max, 70=min = -70 dB) |
| `SVU0x` | 0 | LautstÃ¤rke +3 dB |
| `SVD0x` | 0 | LautstÃ¤rke -3 dB |
| `SRx` | 0â8 | Routing/Quelle setzen (0=Off, 1â8=EingÃ¤nge) |
| `SRU0x` | 0 | Zum nÃ¤chsten Eingang wechseln (deaktivierte Ã¼berspringen) |
| `SRD0x` | 0 | Zum vorherigen Eingang wechseln (deaktivierte Ã¼berspringen) |
| `SM0x` | 0/1 | Mute setzen (0=aus, 1=ein) |
| `SB0x` | 0â14 | Bass setzen (7 = 0 dB neutral) |
| `ST0x` | 0â14 | Treble setzen (7 = 0 dB neutral) |
| `GSV` | â | Firmware-Version abrufen |
| `SAVE` | â | Zoneneinstellungen speichern (gehen sonst beim Ausschalten verloren!) |
| `DEF` | â | â ï¸ Werksreset â alle Zonen- und GerÃ¤teeinstellungen zurÃ¼cksetzen |

Bulk-Befehle werden bevorzugt verwendet; bei Nicht-UnterstÃ¼tzung wird automatisch auf `GZI0x` pro Zone zurÃ¼ckgefallen.

---

## Troubleshooting

**Integration kann keine Verbindung herstellen**
- PrÃ¼fe, ob das MTX-GerÃ¤t per Ping erreichbar ist
- PrÃ¼fe, ob Port 5001 nicht durch eine Firewall blockiert wird
- Das MTX unterstÃ¼tzt nur eine aktive TCP-Verbindung gleichzeitig â andere Clients (z.B. Audac-App) trennen

**Entities zeigen veraltete Werte**
- Der Polling-Intervall betrÃ¤gt 10 Sekunden
- Bei Verbindungsabbruch werden die letzten bekannten Werte beibehalten
- Im HA-Log nach `audac_mtx`-Warnungen suchen

**Lovelace Card wird nicht geladen**
- Manuell unter **Einstellungen** â **Dashboards** â **Ressourcen** prÃ¼fen, ob `/audac_mtx/audac-mtx-card.js` eingetragen ist
- Browser-Cache leeren (Hard Reload / Strg+Shift+R)

---

## Changelog

### 1.7.2
- **Fix:** Sporadischer âKonfigurationsfehler" beim Reload behoben
  - Verschachtelte Template-Literals im CSS-Gradient entfernt (JS Parse-Fehler)
  - `window.customCards` wird beim Laden sofort registriert
  - `connectedCallback()` ergÃ¤nzt fÃ¼r zuverlÃ¤ssiges Re-Render

### 1.7.1
- **Verbesserung:** LautstÃ¤rke-Hintergrundfarbe krÃ¤ftiger (38% Opacity dunkel, 26% hell)
- **Neu:** Akkordeon-Verhalten â nur eine Zone gleichzeitig offen

### 1.7.0
- **Verbesserung:** Zonennamen automatisch gekÃ¼rzt (âAudac MTX Bar" â âBar")
- **Verbesserung:** Untertitel zeigt nur noch Quelle, nicht mehr LautstÃ¤rke in %
- **Verbesserung:** Prozent-Badge rechts entfernt â LautstÃ¤rke als HintergrundfÃ¼llung
- **Verbesserung:** Mute-Badge bleibt sichtbar bei stummgeschalteten Zonen
- **Fix:** `_updateExisting()` mit try/catch â bei Fehler automatischer Rebuild

### 1.6.1
- **Fix:** Zonen nach Reload nicht mehr sichtbar (Timing-Bug: `hass` kam nach `_render()`)
- **Fix:** âCustom element not found" nach Seitenreload (fehlende Versions-URL)
- **Verbesserung:** HACS-Updates aktualisieren Lovelace-Ressource automatisch

### 1.6.0
- **Fix:** Dauerhaftes Flackern der Card behoben
  - Kompletter DOM-Rebuild bei jedem HA State-Update ersetzt durch intelligentes Patching
  - Nur geÃ¤nderte Werte werden aktualisiert (Slider, Badge, Icon, Quelle)
  - Slider werden wÃ¤hrend des Ziehens nicht Ã¼berschrieben
- **Neu:** Bass- und HÃ¶hen-Slider direkt in der Haupt-Card bedienbar

### 1.5.0
- **Fix:** Flackern beim Aufklappen einer Zone (update_entity jetzt verzÃ¶gert nach Animation)
- **Neu:** Bass/HÃ¶hen-Slider in Haupt-Card interaktiv (waren zuvor nur Anzeige)

### 1.4.0
- **Neu:** Sofortige EntitÃ¤ts-Aktualisierung beim Aufklappen einer Zone
- **Neu:** Akzentfarbe frei wÃ¤hlbar im Card-Editor (Farbpalette + Hex-Eingabe + Reset)

### 1.3.1
- **Verbesserung:** Polling-Intervall auf 15 Sekunden erhÃ¶ht (schont TCP-Verbindung)

### 1.3.0
- **Neu:** Befehle `SRU0x` / `SRD0x` implementiert: neue Services `routing_up` / `routing_down`
- **Neu:** Befehl `DEF` (Werksreset) in mtx_client hinzugefÃ¼gt
- **Neu:** Individuelle GET-Befehle `GV0x`, `GR0x`, `GM0x` in mtx_client verfÃ¼gbar
- **Fix:** `SVU0x` / `SVD0x` / `SAVE` / `GSV` senden jetzt korrekt Argument `0` laut Manual
- **Fix:** EingÃ¤nge 7 und 8 korrekt als WandeingÃ¤nge bezeichnet (WLI/MWX65, WMI)
- **Verbesserung:** Protokolldokumentation vervollstÃ¤ndigt (RS232-Parameter, vollstÃ¤ndige Befehlstabelle, 1-Verbindungs-Hinweis)
- **Verbesserung:** Eingangstabelle mit Hardware-Typen ergÃ¤nzt

### 1.2.0
- **Fix:** Doppelter `async_shutdown`-Aufruf beim Entladen der Integration behoben
- **Fix:** Quelle âOff" (Routing 0) ist jetzt standardmÃ¤Ãig in der Quellenauswahl ausgeblendet
- **Fix:** XSS-Schutz in der Lovelace Card â Zonen- und Quellennamen werden korrekt escaped
- **Neu:** Debounce fÃ¼r LautstÃ¤rke-Slider (250 ms) â verhindert unnÃ¶tige Befehle beim Scrollen
- **Neu:** Exponentieller Backoff fÃ¼r Reconnect-Versuche (max. 30 s)
- **Verbesserung:** README mit Troubleshooting, Changelog und Service-Dokumentation ergÃ¤nzt

### 1.1.0
- Modell-Auswahl (MTX48 / MTX88) im Setup-Dialog
- Bulk-Abfragen (GVALL, GRALL, GMALL) mit automatischem Fallback auf Einzelabfragen
- Config-Migration v1 â v2
- Card Editor Ã¼berarbeitet

### 1.0.0
- ErstverÃ¶ffentlichung


---

## Lizenz

MIT License â siehe [LICENSE](LICENSE)


## v1.9.1 – Coordinator-Backoff-Fix & Stabilitätsverbesserungen

### Problembeschreibung (v1.9.0)
Nach dem ersten erfolgreichen Update (durch `async_config_entry_first_refresh()`) liefen keine weiteren Polls mehr. Ursache: Der `asyncio.Lock` wurde während des Startup-Scans (~30–45 s für alle Zonen-Befehle) gehalten. Der 15-s-Scheduler feuerte währenddessen, `COMMAND_TIMEOUT=8 s` lief ab → `UpdateFailed`. Nach 3 aufeinanderfolgenden Fehlern trat der HA-`DataUpdateCoordinator` in exponentiellen Backoff ein und stellte weitere Polls komplett ein.

### Änderungen
- **`mtx_client.py`**: `COMMAND_TIMEOUT` von 8 s → **25 s** erhöht (mehr Spielraum beim Lock-Wait)
- **`coordinator.py`**: `SCAN_INTERVAL` von 15 s → **30 s** erhöht (reduziert Lock-Contention); `UPDATE_TIMEOUT` auf 60 s angepasst; `last_update_success = True` nach jedem erfolgreichen Fetch gesetzt, um Backoff-Zustand explizit zurückzusetzen
- **Ergebnis**: Entities aktualisieren sich zuverlässig alle 30 s ohne Einfrieren

