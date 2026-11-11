# SpeicherPilot inkl. EV-Solver

Speicherplanung (Zendure, SENEC) und EV-Ladeplanung, Solver mit HiGHS

Eigene AppDaemon-Instanz (Basis: Community-App AppDaemon 0.19.3) mit Supervisor-Watchdog und Herzschlag-Wächter.

- Herzschlag: `sensor.speicherpilot_2_letzter_lauf|20` (Entität|Minuten). Bleibt ein Sensor länger still, obwohl HA läuft, startet der Container neu.
- Anlaufzeit nach Start und nach HA-Neustart: 15 min.
- Der App-Code liegt im Konfigordner `/addon_configs/<id>_speicherpilot_solver/apps` und wird separat installiert.
