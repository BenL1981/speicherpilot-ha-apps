## 1.3.13

- Referenz 1.3.1: Ein Fehler beim Veröffentlichen oder Weiterplanen lässt die Rückrechnung nicht mehr dauerhaft als „läuft“ markiert; der nächste Zyklus setzt fort. Auch der Ersatzweg `set_state` kann den Ablauf nicht mehr abbrechen.
- Nachtbewertung 1.3.1: Eine fehlgeschlagene Auswertung wird mit Fehlertyp, Versuchszähler und App-Version gespeichert und nach 10, 30 und 90 Minuten wiederholt statt alle 5 Minuten; danach wird aufgegeben. Der Fehler bleibt im Sensor sichtbar (`auswertungsfehler`), nach einem Update wird neu gerechnet.
- Ex-post 1.2.2: Fehlende Preise sind eine Datenlücke (`DatenUnvollstaendig`, `scheduler_status: daten_unvollstaendig`) mit Angabe der fehlenden Stunde, kein Programmfehler mehr.
- Ex-post 1.2.2: Das Solcast-Archiv wird höchstens einmal täglich nach Alter gekürzt und nur bei Bedarf neu geschrieben (vorher stündlich ab 2 MB). Abgebrochene Zeilen werden vor dem Anhängen abgeschlossen; unlesbare Zeilen werden gezählt (`archiv.unlesbare_zeilen`). Die Kürzung bricht an ungültigen Bytes nicht ab, entfernt Zeilen ohne Zeitstempel und meldet `archiv.alter_geprueft` erst nach gelungener Prüfung.
- Nach dem Update wird die letzte Nacht wegen der neuen Nachtbewertungsversion einmal neu bewertet.

## 1.3.10

- Nachtbewertung 1.3.0 liest die stündliche Solverbegründung mit vollständigen Zeitstempeln und passendem Ursprungsplan.
- Eigene Kategorie und Status `stundenweise_eingeordnet`: Stunden-Momentaufnahmen werden getrennt von laufbezogenen Gegenproben ausgewiesen; keine pauschale wirtschaftliche Bestätigung.
- Fehlende/abgelaufene Pläne, ausgeschaltete Steuerung, Datenlücken und aktuelle Modell-/Umsetzungsfehler bleiben sichtbar.
- Nach dem Update wird die letzte Nacht wegen der neuen Auswertungsversion automatisch erneut bewertet.
- Gemeinsame Prüfung aller drei Apps dauerhaft in AGENTS.md festgehalten und mit einem echten Solver-zu-Auswertung-Vertragstest abgesichert.

## 1.3.4

- Lesbare UTF-8-Protokolle: AppDaemon-ASCII-Umwandlung deaktiviert; persönliche App-Einstellungen bleiben erhalten.
- Auswertungsimage enthält highspy 1.15.1 für Ex-post und prüft die Abhängigkeit beim Start.
- Containerprüfungen rechnen jetzt auch im Auswertungsimage eine echte kleine Optimierung und prüfen Sonderzeichen gegen AppDaemons Logger.
- Dauerhaft fehlgeschlagene Auswertungsjobs werden als fachliche Warnung sichtbar; Liveness- und Hängerüberwachung bleiben aktiv, ohne Neustartschleife bei fachlichen Warnungen.

