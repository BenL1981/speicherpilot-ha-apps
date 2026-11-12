## 1.3.8

- Frische HA-Zustände gezielt über die dokumentierte Template-API abrufen; kein vollständiger Export aller Entitäten im normalen Sicherheitstakt.
- Zeitstempel, Benutzerkontext und Attribute bleiben vollständig erhalten. Bei fehlenden Template-Rechten bleibt der bisherige frische REST-Abruf verfügbar.
- Kommunikationsfehler werden kontrolliert gemeldet; direkter EVCC-Sicherheitsstopp, erneute Befehlszustellung und Fortschrittsüberwachung bleiben aktiv. Keine Verwendung alter Zustände nach einem Timeout.
- HA-Abfragedauer und letzter Kommunikationsfehler sind im Brückensensor sichtbar. Echte Programmfehler bleiben als Rückruffehler erkennbar.
- Unveränderte Wartezeiten: HA maximal 2,5 Sekunden, gemeinsames Arbeitsbudget 7 Sekunden, separater Sicherheitsstopp maximal 1,5 Sekunden.

## 1.3.5

- HA-Zustandsabruf wartet bis zu 2,5 statt 1,5 Sekunden, um kurze Antwortverzögerungen zu überbrücken.
- Gemeinsames 7-Sekunden-Zeitbudget, EVCC-Wartezeiten und direkte Sicherheitsstopp-Anforderung bei fehlgeschlagenem Abruf bleiben erhalten.
- Regression prüft eine erfolgreiche 2-Sekunden-Antwort, verbleibendes Zeitbudget und Sicherheitsstopp nach Timeout.

## 1.3.4

- Lesbare UTF-8-Protokolle: AppDaemon-ASCII-Umwandlung deaktiviert; persönliche App-Einstellungen bleiben erhalten.
- Auswertungsimage enthält highspy 1.15.1 für Ex-post und prüft die Abhängigkeit beim Start.
- Containerprüfungen rechnen jetzt auch im Auswertungsimage eine echte kleine Optimierung und prüfen Sonderzeichen gegen AppDaemons Logger.
- Dauerhaft fehlgeschlagene Auswertungsjobs werden als fachliche Warnung sichtbar; Liveness- und Hängerüberwachung bleiben aktiv, ohne Neustartschleife bei fachlichen Warnungen.

