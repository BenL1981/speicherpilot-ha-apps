# SpeicherPilot EV-Brücke

Setzt den EV-Ladeplan über EVCC um (14-A-Kreis, Reserve-Freigaben, Notstopp)

Eigene AppDaemon-Instanz (Basis: Community-App AppDaemon 0.19.3) mit Supervisor-Watchdog und Herzschlag-Wächter.

- Herzschlag: `sensor.speicherpilot_2_ev_bruecke|5` (Entität|Minuten). Bleibt ein Sensor länger still, obwohl HA läuft, startet der Container neu.
- Anlaufzeit nach Start und nach HA-Neustart: 5 min.
- Der App-Code liegt im Konfigordner `/addon_configs/<id>_speicherpilot_ev_bruecke/apps` und wird separat installiert.
