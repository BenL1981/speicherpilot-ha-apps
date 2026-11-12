## 1.3.5

- HA-Zustandsabruf wartet bis zu 2,5 statt 1,5 Sekunden, um kurze Antwortverzögerungen zu überbrücken.
- Gemeinsames 7-Sekunden-Zeitbudget, EVCC-Wartezeiten und direkte Sicherheitsstopp-Anforderung bei fehlgeschlagenem Abruf bleiben erhalten.
- Regression prüft eine erfolgreiche 2-Sekunden-Antwort, verbleibendes Zeitbudget und Sicherheitsstopp nach Timeout.

## 1.3.4

- Lesbare UTF-8-Protokolle: AppDaemon-ASCII-Umwandlung deaktiviert; persönliche App-Einstellungen bleiben erhalten.
- Auswertungsimage enthält highspy 1.15.1 für Ex-post und prüft die Abhängigkeit beim Start.
- Containerprüfungen rechnen jetzt auch im Auswertungsimage eine echte kleine Optimierung und prüfen Sonderzeichen gegen AppDaemons Logger.
- Dauerhaft fehlgeschlagene Auswertungsjobs werden als fachliche Warnung sichtbar; Liveness- und Hängerüberwachung bleiben aktiv, ohne Neustartschleife bei fachlichen Warnungen.

