## 1.3.17

- Ein manueller Stopp (NRGkick-Ladefreigabe von Hand aus) und seine Ruhezeit bis zur nächsten vollen Stunde überstehen einen Neustart der Brücke. Bisher gab die Brücke das Fahrzeug nach einem Neustart (auch nach dem automatischen Neustart durch den Herzschlag) nach 120 s wieder frei. Grenze: Ein Stopp von Hand, während die Brücke nicht läuft oder gerade neu startet, wird weiterhin nicht erkannt.
- Die Schutzpause nach Netzbezug bei teurem Strom oder SENEC-Entladung (`pv_schutz_bis`) übersteht einen Neustart ebenfalls.
- Reserve-Freigaben: Energie, Kosten und Ende einer Freigabe werden gespeichert. Eine Freigabe, die beim Neustart bekannt war, wird nie fortgesetzt und nie von null neu gezählt; die Brücke meldet dem Solver die bis dahin gelieferte Energie und das Ende „Brückenneustart – Freigabe nicht fortgesetzt“. Lief die Freigabe beim letzten Speichern, wird der Nachlauf weiter gezählt, einschließlich der Energie, die EVCC während des Ausfalls gezählt hat.
- Defekte Notstopp-Datei beim Start: Bisher brach der Start der Brücke mit einem Fehler ab und es wurde nichts mehr gesteuert. Jetzt wird die Datei als `.defekt` beiseitegelegt, alle Fahrzeuge werden wie nach einem Notstopp gesperrt (NRGkick-Ladefreigabe aus, Status `fehler`), bis die EV-Automatik aus- und wieder eingeschaltet wird. Eine fehlende Datei (Neuinstallation) sperrt nichts.
- Defekte Zustandsdatei beim Start: wird als `.defekt` beiseitegelegt; alle Fahrzeuge ruhen bis zur nächsten vollen Stunde wie nach einem manuellen Stopp, und eine bereits genehmigte Reserve-Freigabe wird nicht ausgeführt.
- Neue Datei `speicherpilot_ev_bruecke_state.json` im Zustandsordner; die Notstopp-Datei behält Namen und Format. Neue Attribute im Brückensensor: `zustand_start`, `zustand_speicherfehler`, `notstopp_datei_defekt`. Eine Sperre aus einer defekten Notstopp-Datei trägt in `notstopp_nrgkick` zusätzlich `datei_defekt: true`. Neue optionale Einstellungen `state_max_age_h` (72) und `state_save_interval_s` (60).
- Bestehende Sensoren, Attribute, Einheiten und Statuswerte sind unverändert. Python-Brücke 1.2.13.
- Image-Bau: Die AppDaemon-Basis 0.19.3 (AppDaemon 4.5.13) ist jetzt mit Digest festgelegt; der Inhalt der Basis ändert sich nicht. Port 5050 bleibt unverändert nur im internen Supervisor-Netz erreichbar.

## 1.3.16

- Gemeinsames Speichermodul 1.1 (in allen drei Images): Die Dateisperre der Zustandsdateien wartet beim Speichern höchstens 2 s und bei der Anmeldung beim App-Start höchstens 15 s statt unbegrenzt. Bleibt sie beim Speichern belegt, meldet die Brücke das wie jeden anderen Speicherfehler der Notstopp-Sperre (`notstopp_speicherfehler`, Wächter `stoerung`), die Sperre im Arbeitsspeicher gilt weiter und die Speicherung wird in jedem Takt wiederholt. Eine belegte Sperre hält den Takt damit nicht mehr an.
- Nach dem Speichern wird auch der Ordner auf den Datenträger geschrieben, damit die neue Datei einen Stromausfall übersteht.
- Grenze: Ein hängender Datenträger kann das Öffnen, Schreiben und `fsync` selbst weiter aufhalten; dafür gibt es keine Zeitgrenze.
- Der Python-Code der Brücke (1.2.12), Sensoren, Attribute, Einheiten, Statuswerte und Einstellungen sind unverändert.

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

