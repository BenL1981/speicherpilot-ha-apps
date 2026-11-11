# SpeicherPilot Auswertung

Ex-post, Referenz, Nachtbewertung, Zielerreichung, Quittierung (nur lesend)

Eigene AppDaemon-Instanz (Basis: Community-App AppDaemon 0.19.3) mit Supervisor-Watchdog und Herzschlag-Wächter.

- Herzschlag: `sensor.speicherpilot_referenz_status|30` (Entität|Minuten). Bleibt ein Sensor länger still, obwohl HA läuft, startet der Container neu.
- Anlaufzeit nach Start und nach HA-Neustart: 15 min.
- Ab 1.2.0 enthält das Image den geprüften Python-Code. Updates erfolgen vollständig in HA.
- Persönliche Konfiguration: `/config/apps/apps.yaml`; Laufzeitdaten: `/config/state`.
- Der erste Start sichert den alten Stand unter `/config/.speicherpilot-backups`.
- AppDaemon production_mode ist aktiv: Änderungen der Konfiguration erfordern einen App-Neustart.
