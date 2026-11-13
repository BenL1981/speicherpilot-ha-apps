## 1.3.11

- Der Notstopp schaltet die NRGkick-Ladefreigabe auch dann aus, wenn die Notstopp-Sperre nicht gespeichert werden kann (voller oder schreibgeschützter Datenträger). Ausgeschaltet wird vor dem Speichern, sodass auch ein hängender Datenträger den Stopp nicht verzögert. Die Sperre bleibt im Arbeitsspeicher aktiv, der Speicherfehler wird einmal gemeldet und die Speicherung in jedem Takt wiederholt. Grenze: Eine nicht gespeicherte Sperre geht bei einem Neustart der Brücke verloren, auch bei dem Neustart, den der Container-Wächter wegen des Speicherfehlers auslöst.
- Eine langsame oder fehlgeschlagene Diagnoseveröffentlichung allein stoppt das Laden nicht mehr. Sie wird als Warnung protokolliert, im Status angezeigt (`diagnosefehler_zuletzt`) und im nächsten Takt wiederholt. Fehler beim Lesen von HA oder beim Senden von Ladebefehlen lösen weiterhin den direkten EVCC-Sicherheitsstopp aus.
- Nach einem direkten Sicherheitsstopp bleiben die Ladepunkte für die übliche Stopp-Haltezeit (120 s) aus, statt im nächsten Takt sofort wieder freigegeben zu werden (`sicherheitsstopp_bis`).
- Ein nicht lesbarer Plan eines Fahrzeugs schaltet nur dieses Fahrzeug aus (Grund „Plan ungültig: …“, `plan_ungueltig`); das andere Fahrzeug folgt weiter seinem Plan.
- Bestehende Attribute, Einheiten und Statuswerte bleiben unverändert; es kommen nur Attribute hinzu.

## 1.3.9

- Unveränderte Befehlsauffrischungen werden weiter gesendet und auf DEBUG protokolliert. Neue Befehle und EVCC-Sollabweichungen bleiben auf INFO sichtbar.
- Schwankende HA-Abfragedauern allein lösen keine zusätzliche Statusveröffentlichung aus; der Diagnose-Herzschlag und sofortige fachliche Zustandsänderungen bleiben erhalten.
- PV-Bereitschaft nach dem Anstecken erklärt einen noch älteren, vom getrennten Fahrzeug stammenden Plantext eindeutig.
- Unveränderte Sicherheitsprüfungen, Netzwerkzeitbudgets, Befehlsreihenfolge, Wiederholungen und Ladeentscheidungen.

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

