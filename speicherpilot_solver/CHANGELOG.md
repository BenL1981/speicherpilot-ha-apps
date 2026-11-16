## 1.6

- Gemeinsame Zahlenumwandlung: `as_float` steht einmal in `speicherpilot_zahlen.py` statt in acht Kopien. Für Solver-Kern und Planung unverändert (Zahl, Text, `None`, NaN und ±Unendlich wie bisher).
- Reserve-Modul 1.0.6: `as_float` weist jetzt auch ±Unendlich ab wie alle anderen Module (bisher ließ nur die Reserve-Kopie es durch). Wirkung nur bei unendlichen Werten, im Betrieb bisher nicht beobachtet: Eine Fehlmenge „-inf“ im Entscheidungshelfer hebt nach einer Ablehnung die Sperre für neue Anfragen nicht mehr vorzeitig auf; eine gespeicherte Freigabe mit unendlichem Stundenpreis wird nicht wiederhergestellt („slot_preise unlesbar“); ein unendlicher Preis einer Testladung endet mit „Preis der Stunde fehlt“ statt einem Fehler.
- Aufteilung von `SpeicherPilot2b`, erster Schritt: Geplante Lasten (`speicherpilot2b_lasten.py`) und SENEC-Untergrenze (`speicherpilot2b_senec.py`) liegen in eigenen Modulen, Code zeichengleich, Methoden- und Attributnamen unverändert. `_run_attempt` unverändert.
- Python-App 3.9.2. Keine Sensoren, Attribute, Einheiten oder Statuswerte geändert, keine neue Einstellung; die Versionsattribute zeigen 3.9.2 bzw. Reserve 1.0.6. Gehört zu EV-Brücke 1.4 und Auswertung 1.6 (gleiche Zahlenumwandlung). Einzelheiten in RELEASE_SOLVER_1.6.md.

## 1.5

- Zweitaktplanung bei genehmigter Reserve-Freigabe, die die Mindestreserve nicht ganz erreicht: Die Fahrzeuge wurden an der Anlage weiter in jedem Lauf neu geplant (Nacht 14./15.11.2026: `planungstakte.zaehler` `ev_neu` 64, `haus_aktualisiert` 0, `ausloeser: kritisch`, „id4: Mindestreserve im Plan nicht erreicht“). Ursache: Die Freigabe für den ID.4 war auf 5,98 kWh begrenzt, der Plan nutzte sie vollständig und blieb 0,307 kWh unter der Mindestreserve (ID.4 inzwischen bei 34 % statt 35 %). Jede Restlücke galt als kritisch, auch wenn ein neuer Plan sie nicht schließen kann. Jetzt: Schöpft der gespeicherte Plan die freigegebene Energie aus, wird der Fahrplan wie jeder andere feste Reserve-Fahrplan weitergeführt (`planungstakte.abkuerzung.art: reserve_fahrplan_fest`); die Fahrzeuge werden alle 15 Minuten und bei jeder EV-relevanten Änderung neu geplant, das Haus alle 5 Minuten.
- Weiterhin in jedem Lauf neu geplant wird ein angestecktes Fahrzeug, dessen Plan die Mindestreserve verfehlt, ohne dass die freigegebene Energie ausgeschöpft ist (keine Freigabe, Freigabestunden zu kurz für die freigegebene Energie, nur die Kostengrenze erreicht, Testladung), sowie Pflichtziel und Ziel innerhalb von 15 Minuten.
- Grenze: Bleibt nach ausgeschöpfter Freigabe eine Restlücke, wird zusätzliche reguläre Ladung (PV-Überschuss, Stunden unter der Preisgrenze), die allein durch geänderten Hausverbrauch oder Speicherstand möglich wird, erst mit der nächsten Fahrzeugplanung nach höchstens 15 Minuten erkannt statt nach 5 Minuten. Geänderte Preise, PV-Prognose, Fahrzeug-SoC und Freigabe planen weiter sofort neu.
- Gemessen an der nachgestellten Stunde dieses Falls (zwölf Läufe im Abstand von 5 Minuten, Entwicklungsrechner): Fahrzeugplanungen 12 → 4, Lösungen des Planmodells in der Planung 72 → 48. Jeder der zwölf Pläne ist gleich der vollständigen Rechnung. Nicht an der Anlage gemessen.
- Python-App 3.9.1. Keine Sensoren, Attribute, Einheiten oder Statuswerte geändert; keine neue Einstellung. EV-Brücke 1.3.23 und Auswertung 1.4 bleiben unverändert (Prüfung in RELEASE_SOLVER_1.5.md).

## 1.4

- Versionszählung: Jede App zählt ab jetzt ihre eigenen Schritte. Auf Solver 1.3.22 folgt Solver 1.4 (Python-App 3.9.0). Gehört zu Auswertung 1.4 (gleicher Solver-Kern im Image); die EV-Brücke bleibt 1.3.23.
- Laufzeit mit Fahrzeug im Modell: Die Fahrzeugziele (Reserve, Ziel, Kosten, frühe Stunden, zusätzliche PV) enthalten nur Fahrzeuggrößen. Sie werden jetzt zuerst auf den Fahrzeugzeilen allein gerechnet (einige hundert Spalten statt des ganzen Planmodells). Das Ergebnis wird nur übernommen, wenn derselbe Fahrplan zusammen mit dem Hausoptimum jede Zeile und Grenze des Planmodells erfüllt; dann ist es nachweislich das Optimum der Stufe. Sonst (das Haus braucht die PV oder den Netzanschluss selbst) rechnet ab dieser Stufe das Planmodell wie bisher. Hausstufe und abschließende Gleichstandsstufe laufen immer auf dem Planmodell.
- Gemessen am nachgestellten Fall vom 14.11.2026 (ID.4 mit Reserve-Ausnahme, Q6 nicht angesteckt, 34 Preisstunden, Entwicklungsrechner): Lösungen des Planmodells je vollständigem Lauf 144 → 21 (Planung 36 → 6, Gegenprobe 72 → 12, Puffer 36 → 3), Laufzeit 2,52 s → 0,40 s; ohne Fahrzeug 0,21 s. Nicht an der Anlage gemessen. Plan, Gegenprobe und Puffer sind gleich. Zahlen und Grenzen in RELEASE_SOLVER_1.4.md.
- Pufferprüfung: Der Fahrplan des Plans wurde bisher abgelehnt, wenn HiGHS einen Wert um seine eigene Toleranz über der Grenze geliefert hatte (2,9946009 kW bei 2,9946 kW); dann rechnete das vollständige Fahrzeugmodell. Die Pufferprüfung nutzt jetzt die Toleranz des Solvers (1e-6) und verwendet den Fahrplan neu auch mit Reserve-Ausnahme; passt er nicht, rechnet wie bisher das vollständige Modell (`ev_fahrplan`: `fest` bzw. `neu_geplant`). Dasselbe gilt für Zweitaktplanung und Gegenprobe (nächster Punkt).
- Zweitaktplanung (Fahrzeuge alle 15 Minuten, Haus alle 5 Minuten): Sie lief an der Anlage nie (14.11.2026: `zaehler` `ev_neu` 14, `haus_aktualisiert` 0). Ursache: Jedes gültige Fahrzeug unter seiner Mindestreserve und jede Reserve-Ausnahme galt in jedem Lauf als kritisch, auch ein nicht angestecktes Fahrzeug (ID.4 mit 39 % bei 41 % Reserve, den ganzen Tag). Dazu verwarf die Planung ihren eigenen Fahrplan, wenn HiGHS einen Wert um seine Toleranz über der Grenze geliefert hatte (Prüfung mit 1e-8). Jetzt: Ein nicht angestecktes Fahrzeug ist nicht kritisch (das Anstecken plant sofort neu). Ein angestecktes Fahrzeug unter der Reserve oder mit genehmigter Ausnahme ist nur kritisch, solange der gespeicherte Plan die Reserve nicht erreicht. Der Fahrplan wird mit der Solvertoleranz geprüft (1e-6 kW), auf seine Grenze gesetzt und im aktuellen Planmodell festgeschrieben; passt er nicht, wird im selben Lauf vollständig neu geplant. Pflichtziel und Ziel innerhalb von 15 Minuten bleiben kritisch; die Grenzen 0,5 kWh / 0,05 € und der Rückfall bleiben.
- Was die Fahrzeuge sofort neu plant, steht jetzt im Lauf (`planungstakte.ausloeser`, `planungstakte.geaendert`): Anstecken/Abstecken, Gültigkeit der Fahrzeugdaten, Mindestreserve, Ladeziel, Zielzeit, Ladegrenze, Erteilung, Ende und Restmenge einer Reserve-Freigabe (darüber wirken auch Ausführungsschalter, EV-Automatik und Meldungen der EV-Brücke), Preisgrenze, Gültigkeit des aktuellen Preises, Ladeleistungen, SENEC-Regel, Preistabelle, PV-Prognose, Stundenwechsel, Speicherparameter, Netzgrenze, ein gefallener oder um mehr als 2 %-Punkte gestiegener Fahrzeug-SoC, eine verstrichene Abfahrtszeit, nach 15 Minuten, nach einem Start.
- Genehmigte Reserve-Ausnahme: Ihr Fahrplan wird nach der ersten Planung als fester Fahrplan weitergeführt (`planungstakte.abkuerzung.art: reserve_fahrplan_fest`), bis sich eine der genannten Eingaben ändert, sie endet oder widerrufen wird. Die Grenzen der Freigabe (Stunden, Energie, Kosten) prüft weiter das Modell in jedem Lauf.
- Kein Laden möglich: Hat keine Stunde eine Netzfreigabe, gibt es keine Reserve-Ausnahme und liegt die PV nach Haus und SENEC in jeder Stunde unter der Mindestladeleistung (bisher: genau 0), steht das Ergebnis aus den Eingaben fest. Es wird nur die Hausstufe gerechnet (`ev_ohne_lademoeglichkeit`, Grund in `ev_kein_laden_grund`, `planungstakte.abkuerzung.art: kein_laden_moeglich`). Art und Grund gelten für den Lauf, der sie veröffentlicht, auch bei wiederverwendetem Fahrplan.
- Bindende Netzbezugsgrenze: Lehnt das Planmodell den Fahrplan in zwei Läufen in Folge ab (Hausvorrang; Zendure und Fahrzeug laden in derselben Stunde aus dem Netz und `grid_import_limit_kw` bindet), wird bis zur nächsten EV-relevanten Änderung ohne den Versuch direkt vollständig geplant (`planungstakte.ausloeser: rueckfall_bekannt`). Die Haus-Auffrischung läuft in diesem Fall weiterhin nicht (offener Punkt in RELEASE_SOLVER_1.4.md).
- Fester Fahrplan ohne PV-Anteil: Die zweite Rechnung des Planmodells entfällt; der Plan ist das Hausoptimum mit der Netzleistung der Fahrzeuge obenauf, wenn dieser Punkt jede Zeile des Planmodells erfüllt (`ev_fahrplan_ohne_rechnung`). Sonst wird wie bisher gerechnet.
- Gegenprobe: Sie übernimmt die Matrix des Plans und setzt die erzwungene Entladung als Grenze, statt das Modell je Gerät neu aufzubauen. Die gerechneten Modelle sind dieselben.
- Gemessen an der nachgestellten Stunde (zwölf Läufe im Abstand von 5 Minuten, Entwicklungsrechner, gegenüber dem Stand ohne diese Punkte): ID.4 mit genehmigter Ausnahme 288 → 180 Lösungen des Planmodells, 6,1 s → 3,5 s; kein Fahrzeug angesteckt, PV-Rest unter der Mindestladeleistung 288 → 144, 4,9 s → 2,2 s; zwei ladende Fahrzeuge 288 → 188, 6,6 s → 4,3 s; ohne Fahrzeug 144 → 144, 2,8 s → 2,4 s. Nicht an der Anlage gemessen. Die Pläne sind gleich der vollständigen Rechnung (Grenzen in RELEASE_SOLVER_1.4.md).
- Nach einer Toleranzlockerung (`tolerance_relaxations` ≥ 1) können Stufenwerte gegenüber der Rechnung auf dem Planmodell um rund 1 Wh abweichen (eine erzeugte Variante; erste Stunde, Freigabe und Entlade-Obergrenze gleich).
- Diagnose in `laufzeit_ms`: alle bisherigen Schlüssel bleiben. Neu in `planungstakte`: `ausloeser`, `geaendert`, `ev_soc_grenze_pct`, `abkuerzung` (Art, Grund, seit wann). Neu in `optimierung`: `ev_fahrplan_ohne_rechnung`, `ev_kein_laden_grund`, bei einem Fehler `ev_fahrplan_fehler`, sowie Stufe `ev_vorrechnung` (Aufrufe und Zeit der Rechnung auf den Fahrzeugzeilen), je Fahrzeugstufe `vorab` (Ergebnis von dort, `anzahl` zählt weiter nur Lösungen des Planmodells und kann 0 sein), `ev_stufen_vorab` (Hauptplanung/Nachrechnungen) und bei einem Fehler der Vorrechnung `ev_vorrechnung_fehler`.
- Neue optionale Einstellungen: `solver_ev_vorrechnung` (Vorgabe `true`; `false` rechnet jede Fahrzeugstufe auf dem Planmodell wie bis 1.3.22), `solver_ev_abkuerzungen` (Vorgabe `true`; `false` schaltet „kein Laden möglich“ aus der Mindestladeleistung und den festen Fahrplan ohne zweite Rechnung ab), `ev_soc_replan_delta_pct` (Vorgabe 2,0; kleiner ist erlaubt, größer nicht).
- Keine veröffentlichten Sensoren, Attribute, Einheiten oder Statuswerte entfernt oder umbenannt.
- Stündliche Gegenprobe nahe am Mindest-SoC: Die Energiegrenze der erzwungenen Entladung zieht jetzt die bereits geplante Entladung ab. Bis 3.8.0 verlangte die Gegenprobe dort fast das Doppelte der Restenergie, das Modell war unlösbar und der Speicher wurde als „Entladung nicht zulässig (HiGHS returned Infeasible …)“ gemeldet. Neu: Ursache `minimum` mit dem Text „Restenergie bis zum Mindest-SoC ausgeschöpft“. Gleiche Ursachenklasse (3) für die Nachtbewertung; der Plan war nie betroffen.
- Hinweis: Bei gleich teuren Stunden kann der Optimierer einen Gleichstand je nach Plattform anders auflösen (Linux/x86 gegenüber macOS/arm64: 0,6 kWh zwischen Entladung und Netzbezug in einem Vergleichsszenario, bei gleicher Zielfunktion und gleicher Entscheidung für den laufenden Slot).

## 1.3.22

- Eine genehmigte Reserve-Freigabe übersteht einen Neustart des Solvers: Genehmigte Anträge stehen in der neuen Datei `speicherpilot_ev_reserve_state.json` und werden im ersten Lauf nach dem Start unverändert wiederhergestellt (dieselbe Kennung, dieselben Grenzen und Stunden), wenn der Entscheidungshelfer weiterhin `erlaubt` für genau diese Freigabe enthält, der Helfer seit der Zustimmung nicht neu geschrieben wurde (Home Assistant setzt den Zeitpunkt bei seinem Neustart neu), der gespeicherte Stand höchstens 30 Minuten alt ist und das Fenster nicht abgelaufen ist. Danach gelten die Regeln jeder genehmigten Freigabe (abgesteckt, EV-Automatik aus, Daten ungültig, Widerruf, Meldung der Brücke). Zustimmung wird nie erfunden; Testladungen und offene Anfragen werden nicht gespeichert.
- Hat ein Neustart eine Zustimmung beendet (Meldung der Brücke mit einem Neustart-Grund, verweigerte Wiederherstellung im Zweifel, Neustart von Home Assistant), sucht der Solver sofort ein neues Fenster statt nach 30 Minuten. Der Push-Text der Ersatzanfrage beginnt mit „Die vorherige Freigabe wurde durch einen Neustart beendet – bitte erneut zustimmen.“ Der Hinweis steht nur in einer Anfrage, die binnen 30 Minuten nach diesem Ende erstellt wurde.
- Ein Neustart von Home Assistant verwirft Anfragen und Freigaben wie bisher, auch wenn der Solver erst danach startet und das Ereignis nie sieht (erkannt am Änderungszeitpunkt des Entscheidungshelfers).
- Startet auch die Brücke neu, wartet eine gerade wiederhergestellte Freigabe einmal bis zu 180 s auf deren ersten Bericht, bevor „EV-Brücke meldet sich nicht“ gilt.
- Neue Attribute: `wiederhergestellt_um` und `letztes_ergebnis.neustart` am Reserve-Sensor, `reserve_wiederherstellung_offen` und `reserve_ausnahme.freigabe_gueltig_bis` am Fahrzeugplan. Neue optionale Einstellungen `restore_max_age_minutes` (30) und `decision_change_tolerance_s` (60) unter `ev.reserve_approval`.
- Plan, Empfehlungen, Solverstatus und bestehende Sensoren, Attribute, Einheiten und Statuswerte sind unverändert. Python-App 3.8.0, Reserve-Modul 1.0.5. Gehört zu EV-Brücke 1.3.23; Einzelheiten in RELEASE_1.3.22.md.

## 1.3.19

- Abgezogene Lasten (`load_exclude`) ohne ausreichende Messhistorie, etwa eine neu eingebundene Steckdose: Der Hinweis in der Datenqualität lautet neu „Excluded load not removed from load profile yet (history still building): …“ statt „EV charging not removed from load profile: …“. Echte Fehler (ungültige Werte, fehlgeschlagener Verlaufsabruf) heißen „Excluded load not removed from load profile: …“. Die Datenqualität bleibt in beiden Fällen `ok`.
- Protokoll: Eine nicht abgezogene Last wird nur noch einmal je Grund gemeldet statt bei jeder Aktualisierung des Lastprofils; fehlende Historie als Information, echte Fehler weiterhin als Warnung. Sobald die Last abgezogen wird, steht das einmal im Protokoll.
- Neues Feld `history_building: true` am betroffenen Eintrag in `load_profile.excluded_loads`. Der Abzug selbst, die Schwelle (80 % der Hausdaten, keine fehlende Stunde) und der Plan sind unverändert.
- Image mit per Prüfsumme festgelegtem Basis-Image (siehe RELEASE_1.3.17.md, Teil F). Python-App 3.7.1.

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
