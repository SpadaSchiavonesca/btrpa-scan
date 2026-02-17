# GUI Design: Web-Based Radar Interface for btrpa-scan

**Date:** 2026-02-17

## Summary

Add a `--gui` flag that launches a local Flask + WebSocket web UI featuring an animated radar sweep visualization showing device distances, an embedded Leaflet.js map panel for GPS data, a device table, and hover tooltips with full device details. Dark "hacker aesthetic" theme.

## Architecture

- **Backend:** Flask + flask-socketio running in a thread alongside the BLE scanner
- **Frontend:** Single HTML page (embedded in btrpa-scan.py as a string template) with Canvas radar + Leaflet.js map
- **Data flow:** WebSocket push on every detection callback; REST `/api/state` for initial load and reconnection
- **Launch:** `--gui` flag auto-opens browser; `--gui-port N` for custom port (default 5000)

## WebSocket Events

| Event | Payload | Frequency |
|-------|---------|-----------|
| `device_update` | address, name, rssi, avg_rssi, tx_power, est_distance, lat, lon, manufacturer_data, service_uuids, seen_count, timestamp | Per detection (debounced ~10/sec/device) |
| `gps_update` | lat, lon, alt | ~2s interval |
| `scan_status` | elapsed, total_detections, unique_count, scanning | ~1s interval |
| `scan_complete` | summary stats | On scan end |

## UI Layout

- **Header bar:** Logo, device count, detection count, elapsed time, scan settings
- **Main area (split):** 60% radar canvas (left), 40% Leaflet map (right). Map hidden if no GPS — radar goes full-width
- **Radar:** Concentric distance rings (1m, 5m, 10m, 20m), rotating green sweep line with fade trail, color-coded pulsing device dots
- **Device table (below):** Sortable columns — address, name, RSSI, distance, seen count, last seen. Row hover highlights radar dot
- **Hover tooltip:** Full device details (address, name, RSSI, avg RSSI, TX power, distance, GPS, manufacturer, services, seen count)

## Visual Style

- Dark theme: black/dark gray background, green/cyan accents
- Radar sweep: bright green line rotating 360° every ~4s with fading trail
- Device dots: green (strong signal), yellow (medium), red (weak). Pulse on new detection
- Smooth CSS transitions for position updates

## Radar Positioning Logic

- Angle: deterministic hash of MAC address (stable position)
- Distance from center: proportional to est_distance with logarithmic scaling
- No distance estimate: placed at outer ring periphery labeled "unknown"

## Error Handling

- Port conflict: auto-increment 5000-5010
- Browser open failure: print URL to console
- `--gui` + `--tui` mutually exclusive (error)
- WebSocket disconnect: auto-reconnect + resync via `/api/state`
- No GPS: map hidden, GPS fields omitted from tooltips
- No TX Power / no distance: dot at periphery, "Unknown" label
- Ctrl+C: graceful shutdown of scanner + Flask server, "Scan complete" overlay in browser

## Performance

- WebSocket debounce: max ~10 emissions/sec/device
- Canvas: 30fps via requestAnimationFrame
- DOM: recycle table rows, don't rebuild

## Dependencies Added

```
flask>=3.0.0
flask-socketio>=5.3.0
```

## CLI Additions

```
--gui          Launch web-based graphical interface (opens browser)
--gui-port N   Port for GUI server (default: 5000)
```

## File Structure

All HTML/CSS/JS embedded as a Python string in btrpa-scan.py (maintains single-file architecture).
