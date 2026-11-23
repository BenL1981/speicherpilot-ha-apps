## 1.7

- Nachtbewertung 1.3.5: Die Stunden-Momentaufnahme ordnet auch dann ein, wenn der Solver die Entscheidungszeile des Stundenwechsel-Laufs wegen seiner 60-Sekunden-Grenze nicht geschrieben hat (5-Minuten-Lauf kurz vor hh:00). Der Lauf erbt dann die Diagnose des Laufs davor (gleiche Solver-Version, höchstens 90 s); sie muss der Ursache der Momentaufnahme entsprechen. Bisher wurde der Netzbezug solcher Stunden zu Unrecht als „nicht zuordenbar“ gebucht (Nacht 22./23.11.2026: 3,93 kWh, Meldung „prüfbedarf“).
- Die letzte Nacht wird nach der Installation einmal neu bewertet (neue `APP_VERSION`). Referenz 1.3.2, Ex-post 1.2.3, Zielerreichung 1.1.3, Quittierung 1.1 unverändert.
- Keine Sensoren, Attribute, Einheiten oder Statuswerte geändert. Einzelheiten in RELEASE_AUSWERTUNG_1.7.md.

## 1.6

- Gemeinsame Zahlenumwandlung: Referenz, Ex-post, Nachtbewertung, Zielerreichung und Quittierung nutzen `as_float` aus `speicherpilot_zahlen.py` statt eigener Kopien. Verhalten unverändert (geprüft gegen jede frühere Kopie). Das gilt auch für den mitgelieferten Solver-Kern (`speicherpilot2b_core.py`), den Ex-post zum Nachrechnen aufruft.
- Toter Code ohne Aufrufer entfernt: `dataset_from_export`, `rle_decode` und `SERIES_KW` in `speicherpilot_expost_core.py` (Format des früheren Browser-Exports) sowie `taper_cap_kw` im Solver-Kern. Verhalten unverändert.
- Keine App-Version geändert: Referenz 1.3.2 (`SIM_VERSION` 1.2), Ex-post 1.2.3 (`EXPOST_VERSION` 1.0.0), Nachtbewertung 1.3.4, Zielerreichung 1.1.3, Quittierung 1.1. Es wird daher nichts neu berechnet oder neu bewertet.
- Keine Sensoren, Attribute, Einheiten oder Statuswerte geändert, keine neue Einstellung. Gehört zu Solver 1.6 und EV-Brücke 1.4. Einzelheiten in RELEASE_AUSWERTUNG_1.6.md.

## 1.5

- Referenz 1.3.2: Der Verlauf eines Rechenabschnitts kommt mit einer einzigen Abfrage an Home Assistant statt mit einer je Sensor (16). Im laufenden Betrieb sind das 288 statt 4 608 Verlaufsabfragen am Tag. Gefragt wird genau so wie bisher; die Werte sind dieselben. Scheitert die gebündelte Abfrage, fragt die Referenz wie bisher je Sensor. Neues Attribut `verlauf_abfrage` am Statussensor: `ha_rest_gebuendelt` oder `appdaemon_einzeln (Grund)`.
- Referenz 1.3.2: Am Ende jedes 5-Minuten-Zyklus wird einmal veröffentlicht statt zweimal; der Status springt nicht mehr kurz auf „backtest 100 %“. Die Sensoren werden nur noch gesendet, wenn sich Zustand oder Attribute geändert haben, sonst spätestens nach 60 s (Einstellung `heartbeat_seconds`). Gemerkt wird nur ein über REST bestätigtes Senden; über den Rückfall `set_state` wird jedes Mal gesendet wie bisher. `berechnet_am` zählt nicht als Änderung, wird aber bei jedem Senden neu gesetzt; der Herzschlag des Containers (`last_updated` von `sensor.speicherpilot_referenz_status`, Grenze 30 min) bleibt so frisch wie bisher.
- Zielerreichung 1.1.3: Die zehn Sensoren werden nur noch gesendet, wenn sich ihr Inhalt geändert hat, sonst spätestens nach 60 s (`heartbeat_seconds`, geprüft mit 5 s Spielraum für den schwankenden 30-s-Takt). Bisher sendete jeder 30-s-Takt alle zehn unverändert (28 800 Anfragen am Tag). `last_updated` ändert sich dadurch nicht, weil Home Assistant ein unverändertes Schreiben ohnehin nicht als Änderung zählt.
- Ex-post, Nachtbewertung und Quittierung sind unverändert. Keine geänderten oder entfernten Sensoren, Attribute, Einheiten oder Statuswerte. Einzelheiten in RELEASE_AUSWERTUNG_1.5.md.

## 1.4

- Versionszählung: Jede App zählt ab jetzt ihre eigenen Schritte. Auf Auswertung 1.3.21 folgt Auswertung 1.4.
- Das Image enthält den Solver-Kern von Solver 1.4 (`speicherpilot2b_core.py`), den die Ex-post-Gegenrechnung für ihre Nachrechnung aufruft. Geändert ist dort die Rechnung mit Fahrzeugen im Modell; im Zweig ohne Fahrzeuge, den die Ex-post-Nachrechnung durchläuft, wird nur die Versionsabfrage von highspy je Prozess einmal statt je Ergebnis gelesen, und die Untergrenze der Gegenprobe (`force_discharge_kw`, von Ex-post nicht benutzt) wird nach dem Modellaufbau gesetzt. Gespeicherte und neue Ex-post-Ergebnisse bleiben vergleichbar (zehn Rechnungen bytegleich mit dem Kern aus 1.3.21), `EXPOST_VERSION` bleibt 1.0.0, es wird nichts neu bewertet.
- Ex-post, Nachtbewertung, Referenz, Zielerreichung und Quittierung sind unverändert. Keine neue Einstellung, keine geänderten Sensoren oder Attribute. Einzelheiten in RELEASE_AUSWERTUNG_1.4.md.

## 1.3.21

- Nachtbewertung 1.3.4: Die Größe der Attribute von `sensor.speicherpilot_nachtbewertung` wird jetzt so gezählt, wie der Recorder von Home Assistant sie zählt (JSON ohne Leerzeichen, UTF-8). Bisher zählte die App rund 10 % mehr und kürzte Nächte, die noch weit unter der Recorder-Grenze von 16 384 Byte lagen. Die eigene Kürzungsgrenze bleibt 15 000 Byte. Testnacht mit vollständigem Ex-post-Verweis: 13 440 Byte, 1 560 Byte Abstand (vorher 47 Byte in der alten Zählung).
- Neues Attribut `groessenstufe`: `vollstaendig`, `verweis_kurz`, `verweis_entfallen`, `stunden_gekuerzt`, `verlauf_gekuerzt` oder `notfall_gekuerzt`. Ab `stunden_gekuerzt` steht wie bisher zusätzlich `groessenhinweis`.
- Zwei weitere Stufen für Nächte, die auch mit gekürzten Stunden zu groß sind (bisher wurden sie trotzdem veröffentlicht und der Recorder speicherte dann keine Attribute): zuerst entfallen die ältesten Verlaufseinträge (mindestens die sieben neuesten bleiben). Reicht das nicht, werden in der letzten Stufe schrittweise und jeweils neu gemessen Warnungen, Prüfgründe und Texte begrenzt, dann der Verlauf weiter gekürzt, dann die Stunden und zuletzt die Vorfälle der Nacht geleert; `groessenhinweis` nennt, was gekürzt wurde. Alle Schlüssel, der Status, die kWh-Werte, die Geräteblöcke, beide Bewertungen und die offenen Vorfälle (`offene_vorfaelle`) bleiben in jeder Stufe.
- Kein Schlüssel und kein Wert wurde entfernt, umbenannt oder umformatiert. Keine neue Einstellung.
- Nach dem Update wird die letzte Nacht wegen der neuen Nachtbewertungsversion einmal neu bewertet.

## 1.3.18

- Nachtbewertung 1.3.3: `wirtschaftliche_bewertung.ex_post` verweist jetzt auf die Ex-post-Gegenrechnung desselben Containers. Neues Feld `gegenrechnung` mit Quelle (`sensor.speicherpilot_expost`), Status (`vorlaeufig`/`final`), Abstand zum Optimum, Entscheidungsqualität, Modellunsicherheit und dem Hinweis, dass das Ex-post-Fenster Nacht + Folgetag umfasst. Liegt kein Ergebnis für diese Nacht vor, steht dort `kein_ergebnis`, `andere_nacht`, `fehler`, `nicht_auswertbar` oder `nicht_lesbar`, ohne Zahlen. `fehler` steht dort auch, wenn der Ex-post-Lauf dieser Nacht scheiterte, bevor ein Ergebnis vorlag (Attribut `fehler.nacht` des Ex-post-Sensors).
- Der Statuswert `nicht_belastbar_berechenbar` und alle Schlüssel bleiben. Der Text `grund` behauptet nicht mehr, die Neuberechnung finde „nicht in der App“ statt, und ist nicht länger als der alte. Der Nachtstatus hängt nie vom Ex-post-Ergebnis ab.
- Der Verweis kostet keine Stundendetails: Passt er nicht unter die Kürzungsgrenze von 15 000 Byte, wird er auf Status und Abstand gekürzt oder weggelassen. Ein Verweis, dessen Veröffentlichung fehlschlug, wird mit der nächsten Ex-post-Aktualisierung nachgeholt.
- Ex-post 1.2.3: `sensor.speicherpilot_lernstatus` → `solcast_guete.kalibrierung` beschreibt den vorhandenen PV-Kalibrierungskandidaten `pv_bias` (Faktor aus Vortagen, Grenzen, Trefferquoten der skalierten P10/P50, nötige Solver-Option). Kein neuer Kandidat, keine zusätzliche Rechenzeit, keine Parameterübernahme (Stufe A unverändert).
- Neue Einstellung der Nachtbewertung: `expost_sensor` (Voreinstellung `sensor.speicherpilot_expost`).
- Nach dem Update wird die letzte Nacht wegen der neuen Nachtbewertungsversion einmal neu bewertet. `kalibrierung` erscheint mit der nächsten finalen Ex-post-Auswertung (22:20 Uhr).
- Image-Bau: Die AppDaemon-Basis 0.19.3 (AppDaemon 4.5.13) ist jetzt mit Digest festgelegt; der Inhalt der Basis ändert sich nicht. Port 5050 bleibt unverändert nur im internen Supervisor-Netz erreichbar.

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

