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
