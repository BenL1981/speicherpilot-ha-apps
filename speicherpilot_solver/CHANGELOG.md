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
