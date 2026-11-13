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

