# SpeicherPilot Auswertung

Ex-post, Referenz, Nachtbewertung, Zielerreichung, Quittierung (nur lesend)

Eigene AppDaemon-Instanz (Basis: Community-App AppDaemon 0.19.3) mit Supervisor-Watchdog und Herzschlag-Wächter.

- Herzschlag: `sensor.speicherpilot_referenz_status|30` (Entität|Minuten). Bleibt ein Sensor länger still, obwohl HA läuft, startet der Container neu.
- Anlaufzeit nach Start und nach HA-Neustart: 15 min.
- Der App-Code liegt im Konfigordner `/addon_configs/<id>_speicherpilot_auswertung/apps` und wird separat installiert.
