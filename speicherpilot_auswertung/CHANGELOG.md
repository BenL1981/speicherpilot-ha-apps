## 1.3.14

- Nachtbewertung 1.3.2: Eine vollständig aufgezeichnete Stunden-Momentaufnahme mit Ursache 2 (SoC-Reserve) ordnet spätere gültige Halten-Pläne derselben Stunde ohne eigene Diagnose ein, unter denselben Bedingungen wie bisher bei Ursache 1. Vorher blieb dieser Netzbezug „nicht zuordenbar“ (Nacht zum 14.11.2026: 2,35 von 7,33 kWh, Status `pruefbedarf`).
- Diese kWh bleiben in `stundenweise_eingeordnet` und zählen weder zu `reserve` noch zu `wirtschaftlich`. Neu ist die Aufteilung nach Ursache der Momentaufnahme: `stundenweise_eingeordnet_reserve_kwh`, `stundenweise_eingeordnet_wirtschaftlich_kwh`, `nach_ursache_kwh`, je Stunde `stundenbegruendung.ursache_nr` (Detailbericht zusätzlich `stundenweise_nach_ursache_kwh`), im Verlauf `sr` (nur wenn größer 0).
- Der Status einer solchen Nacht ist `stundenweise_eingeordnet`, nie `wirtschaftlich_plausibel`; die Kurzbegründung nennt den Reserve-Anteil.
- Andere Ursachen, übersprungene oder fehlgeschlagene Gegenproben, vom Solver auf Eingangsdaten umbenannte Diagnosen, ausgeschaltete Steuerung und Planlücken bleiben unerklärt bzw. in ihrer bisherigen Kategorie.
- Strenger für Ursache 1 und 2: Spätere Läufe mit `datenqualitaet: degraded` ohne eigene Diagnose werden nicht durch die Momentaufnahme eingeordnet; der Ursprungslauf braucht eine eigene Entscheidungszeile (keine geerbte Diagnose); eine neuere, nicht zugelassene Momentaufnahme derselben Stunde beendet die Wirkung der älteren.
- Wird der Sensor wegen seiner Größe gekürzt, behält jede Stunde ihre `stundenbegruendung` (Quelle, Zeitpunkt, Ursache der Momentaufnahme).
- Nach dem Update wird die letzte Nacht wegen der neuen Nachtbewertungsversion einmal neu bewertet.
- Das Image enthält das gemeinsame Speichermodul 1.1 (Solver 1.3.15, EV-Brücke 1.3.16): Dateisperre mit Zeitgrenze (2 s beim Speichern, 15 s bei der Anmeldung beim App-Start) statt unbegrenztem Warten, Ordner-`fsync` nach dem Speichern. Eine belegte Sperre erscheint als „Zustand nicht gespeichert“ (Wächter `stoerung`) und wird beim nächsten Speichern wiederholt.

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

