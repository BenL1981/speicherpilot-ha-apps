## 1.3.15

- SENEC-Altersprüfung (`senec_soc_max_age_minutes`, weiterhin aus): liest das echte `last_reported` über die HA-Template-API statt über `/api/states`, dessen Zeitstempel bei unverändertem Wert stehen bleibt (1.3.12 zog damit auf der Anlage den Plan zurück). „stale“ nur noch, wenn der unveränderte Wert nachweislich mehrfach erneut gemeldet wurde (drei Meldungen, Alter über dem Dreifachen ihres größten Abstands) und eine zweite Lesung den alten Zeitstempel bestätigt; jede fehlgeschlagene Lesung ergibt `range_only`. Quelle in `soc_pruefung`: `last_reported_template`, neues Feld `hinweis`.
- Laufbezogene Gegenprobe wird nachgerechnet, wenn die zwischengespeicherte nicht mehr zum Lauf passt, höchstens alle `hold_diagnosis_min_interval_s` (neu, Vorgabe 900 s) und nur mit ausreichendem Zeitbudget; sonst `uebersprungen`. Eine gescheiterte Nachrechnung gilt nur für ihren Lauf (`fehlgeschlagen`) und wird nicht zwischengespeichert. Ist `solver_run_budget_s` nicht größer als `solver_fallback_budget_s`, wird nicht nachgerechnet (Warnung beim Start). Die Stunden-Momentaufnahme (`stundenbegruendung`) bleibt die erste Rechnung der Stunde und wird nie überschrieben. Neues Feld `gegenprobe_status` in `laufzeit_ms`.
- Stunden-Momentaufnahme trägt neu `suboptimal` (true, wenn ihr Plan am Zeitlimit übernommen wurde).
- REST-Pause: Nach zwei unbeantworteten REST-Aufrufen in Folge setzt der Solver REST für `ha_rest_cooldown_s` (neu, Vorgabe 60 s) aus und nutzt sofort die Rückfallpfade; die erste Antwort beendet die Pause. Die erste Veröffentlichung jedes Laufs und jedes Planrückzugs versucht REST immer. Kehrt während der Pause auch AppDaemon `set_state` erst an seinem 10-s-Zeitlimit zurück, werden die übrigen Veröffentlichungen ausgelassen, bis Home Assistant antwortet oder der nächste Lauf beginnt (nie die steuerungsrelevanten Sensoren eines Rückzugs). Mit hängendem Home Assistant wartete ein Lauf bisher über 100 s, jetzt rund 15–30 s (Rückzug: zusätzlich bis 10 s je steuerungsrelevantem Sensor). Zähler im neuen Attribut `veroeffentlichung` der Datenqualität.
- Rückfall auf AppDaemon `set_state` (verliert `true`/`false`/`0`, die EV-Brücke lehnt die Pläne dann ab) wird für steuerungsrelevante Sensoren gemeldet: Wächter `warnung`, Datenqualität `degraded`. Der Pfad selbst ist unverändert.
- Gemerkte SENEC-Untergrenze übersteht Neustarts (`speicherpilot_senec_untergrenze.json`, gültig für `merken_stunden`).
- Neuer Sensor `sensor.speicherpilot_2_letzter_versuch`: Zeit, Ergebnis und Fehlerklasse (`eingangsdaten`, `planung`, `programmfehler`) jedes Solverversuchs; ein gescheiterter Lauf erscheint beim Wächter als `warnung`. `letzter_lauf` und der Herzschlag sind unverändert.
- Defekte Zustandsdateien (Stundenbegründung, EV-Budget, Untergrenze) werden als `.defekt` gesichert und neu geschrieben (Stundenbegründung: Schreibprobe, danach `warnung` bis zur nächsten Momentaufnahme); Lese- und Schreibfehler meldet der Wächter als `stoerung`, bis wieder gespeichert wurde. Scheitert nur die Anmeldung als Schreiber, bleibt die Datei unverändert liegen.
- Planrückzug versucht jeden Sensor einzeln und bei Fehlern den anderen Veröffentlichungspfad; ein unvollständiger Rückzug wird gemeldet. Mit `publish_mode: ha_rest` gilt ein steuerungsrelevanter Sensor nur als zurückgezogen, wenn REST ihn angenommen hat; sonst `rueckzug_unbestaetigt` am letzten Versuch und Wächter `warnung` (AppDaemon `set_state` meldet einen Fehlschlag nicht).
- Gemeinsames Speichermodul 1.1 (in allen drei Images): Die Dateisperre der Zustandsdateien wartet beim Speichern höchstens 2 s und bei der Anmeldung beim App-Start höchstens 15 s statt unbegrenzt; eine belegte Sperre erscheint als Speicherfehler der betroffenen Datei (Wächter `stoerung`, bis wieder gespeichert wurde) und der Lauf geht weiter. Nach dem Speichern wird auch der Ordner auf den Datenträger geschrieben. Grenze: Öffnen, Schreiben und `fsync` selbst können auf einem hängenden Datenträger weiter blockieren.
- Vorlage `appdaemon.yaml` für neue Installationen: Zeitzone `Europe/Berlin` (gleiche Uhrzeit wie bisher `Europe/Amsterdam`). Bestehende Installationen werden nicht verändert. Admin-Oberfläche und API von AppDaemon bleiben unverändert eingeschaltet (siehe Releasenotiz, offener Punkt).
- Python-App 3.7.0. Keine veröffentlichten Sensoren, Attribute, Einheiten oder Statuswerte entfernt oder umbenannt; der Diagnosewert `last_reported_rest` (nur bei eingeschalteter SENEC-Altersprüfung) entfällt mit dem REST-Lesepfad.

## 1.3.12

- Zeitbudget je Solverlauf (`solver_run_budget_s`, Vorgabe 150 s): Jede Optimierung erhält höchstens die Restzeit. Ist das Budget verbraucht, werden Gegenprobe und Pufferprüfung übersprungen (`uebersprungen`, kein Fehler); der Plan für Haus und Speicher erhält weiterhin eigene Rechenzeit (`solver_fallback_budget_s`, Vorgabe 45 s).
- Eine zulässige Lösung am HiGHS-Zeitlimit wird übernommen, wenn die relative Lücke höchstens 0,1 % beträgt (`mip_accept_gap`). Neues Attribut `suboptimal` am Solverstatus; größere Lücken führen wie bisher in die Rückfallkette. Die Gegenprobe meldet für einen solchen Plan innerhalb seiner Lücke keinen Modellfehler (Ursache 5).
- Speicher-SoC außerhalb 0–100 % und alte Zendure-Werte eines nicht erreichbaren Geräts sind ungültig: Der Plan wird zurückgezogen. Ein stundenlang gleicher SoC bleibt gültig, solange das Gerät online ist. Neues Attribut `soc_pruefung` an der Datenqualität. Die wahlweise SENEC-Altersprüfung liest `last_reported` über HA-REST.
- SoC- und Solcast-Änderungen lösen höchstens alle 120 s eine Neuplanung aus, außer bei SoC-Sprüngen ab 2 Prozentpunkten oder Ausfall eines Werts. Preis, Stecker, Entscheidungen, Schalter und die regelmäßigen Läufe (alle 300 s, Stundenwechsel) bleiben unverändert.
- Die Pufferprüfung rechnet mit dem EV-Fahrplan des Plans nur noch die Hausstufe; passt der Fahrplan nicht zur erhöhten Last, rechnet sie wie bisher das vollständige EV-Modell.
- Python-App 3.6.0. Keine veröffentlichten Attribute, Einheiten oder Statuswerte entfernt oder umbenannt.

## 1.3.7

- Identische Modellmatrizen werden innerhalb einer rekursiven Solverberechnung frisch kopiert, statt sie für jede Reserve-Nachrechnung erneut aufzubauen.
- Puffer, Hauptplanung und stündliche Gegenprobe rechnen weiterhin alle Optimierungen mit aktuellen Eingaben; keine Freigaben oder Lösungen werden zwischengespeichert.
- Alle physikalischen Eingaben bleiben Teil des Vergleichsschlüssels. Reservekosten und Netzladekappen werden auf die neue Modellkopie angewendet; abgelehnte Kopien führen zum vollständigen Aufbau.
- Getrennte Messwerte für vollständigen Modellaufbau und Modellkopien. Stundenbegründung aus 1.3.6 bleibt aktiv.

## 1.3.6

- Wirtschaftliche Gegenprobe als stündliche Momentaufnahme statt erneuter Optimierung bei jeder Eingangsänderung.
- Eigener Sensor `sensor.speicherpilot_2_stundenbegruendung` mit Zeitfenster, Berechnungszeitpunkt, ursprünglichem Plan und den letzten 24 erfassten Stunden.
- Stundenbegründung bleibt über App-Neustarts erhalten; geänderte Eingänge werden nicht mit alter wirtschaftlicher Evidenz als frisch bestätigt.
- Haus-/EV-Planung, Pufferberechnung, Reservebedingungen und aktuelle Umsetzungsprüfung bleiben unverändert.

## 1.3.4

- Lesbare UTF-8-Protokolle: AppDaemon-ASCII-Umwandlung deaktiviert; persönliche App-Einstellungen bleiben erhalten.
- Auswertungsimage enthält highspy 1.15.1 für Ex-post und prüft die Abhängigkeit beim Start.
- Containerprüfungen rechnen jetzt auch im Auswertungsimage eine echte kleine Optimierung und prüfen Sonderzeichen gegen AppDaemons Logger.
- Dauerhaft fehlgeschlagene Auswertungsjobs werden als fachliche Warnung sichtbar; Liveness- und Hängerüberwachung bleiben aktiv, ohne Neustartschleife bei fachlichen Warnungen.

## 1.3.3

- Wirtschaftliche Gegenprobe nutzt identische, bereits berechnete Nullreserve-Zielwerte innerhalb desselben Durchlaufs erneut.
- Alle physikalischen Eingaben einschließlich EV-Fahrplan und erzwungener Entladung müssen exakt passen; sonst wird normal gerechnet.
- Eigene Protokollangabe: Gegenprobe in Millisekunden, frisch oder aus dem Diagnose-Cache.
- Bisherige Diagnosehäufigkeit und sämtliche Steuerungs-/Pufferprüfungen bleiben bestehen.
- 36 lokale Vergleiche mit identischen Erklärungen, Plänen und Puffern; etwa 20–25 Prozent kürzere Gegenprobe in den berechneten Testfällen.

## 1.3.2

- Kürzere Puffer- und Hauptplanung: fertige Solver-Lösung nur einmal vollständig auslesen.
- EV-Rechenschritte entfallen ausschließlich bei exakt nachgewiesener fehlender Lademöglichkeit im gesamten Horizont und ohne Reserve-Ausnahme.
- Haus- und Speichergrenzen sowie alle Lastspitzen-Gegenproben bleiben frisch; bei möglichen Ladungen bleibt der vollständige EV-Rechenweg aktiv.
- Getrennte Zähler zeigen die genutzte Abkürzung in Hauptplanung und Nachrechnungen.
- 72 lokale Vergleichsläufe mit exakt gleichen vollständigen Plänen und Puffergrenzen; zusätzliche Sicherheits- und Auswertungsprüfungen.

## 1.3.1

- Bewegliche Zeitangaben geplanter Haushaltsgeräte und frische Hauslasten lösen keine unnötige EV-Neuplanung mehr aus.
- Diese Lasten werden bei jeder Hausaktualisierung vollständig im aktuellen physikalischen Modell berücksichtigt; bei Unzulässigkeit folgt die gemeinsame Neuplanung.

## 1.3.0

- Zwei Planungstakte: EV-Fahrplan regulär alle 15 Minuten; Haus und Speicher dazwischen mit frischer Prüfung des EV-Fahrplans.
- Sofortiger gemeinsamer Rückfall bei unzulässigen Plänen, gefährdeten Reserven oder überschrittenen Qualitätsbudgets.
- Komfortobergrenze 0,5 kWh und separat höchstens 0,05 EUR bewerteter Nachteil je Auto und Abfahrtsfenster.
- Dauerhaft gespeichertes Abweichungsbudget verhindert erneute Gewährung durch Neuplanung oder Neustart.
- Verbindliche Ziele, aktive Reserve-Ausnahmen und die letzten 15 Minuten vor dem Ladeziel bleiben beim gemeinsamen Solver.
- Getrennte Zeitstempel, Planungstakte, Rückfälle und Budgetverbrauch sind in den EV-Sensoren sichtbar.
- Preisgrenzen, gemeinsamer Stromkreis, EV-Brücke und Entladepuffer bleiben geschützt.

## 1.2.4

- Schnellere gemeinsame Planung durch Weitergabe bereits berechneter Lösungen zwischen EV-Prioritätsstufen.
- Reserve-Ausnahmen behalten den bisherigen, im Vergleich bestätigten Rechenpfad.
- Zusammenführung kurz aufeinanderfolgender regelmäßiger Läufe; wichtige Eingaben und Stundenwechsel bleiben berücksichtigt.
- Getrennte Laufzeitmessung je Optimierungsstufe und vollständige Gesamtdauer im Protokoll.
- Reproduzierbare Vergleichstests für Planungsgüte, Entladepuffer und Prozessorbelegung.
- Die bisherige gemeinsame Planung und der Sicherheitsrhythmus der EV-Brücke bleiben erhalten.

## 1.2.3

- Vollständige Updates einschließlich Python-Code, geprüfter Datenpfad und Fortschrittsüberwachung.
