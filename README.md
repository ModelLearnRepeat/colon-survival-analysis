# Überlebenszeitanalyse bei Kolonkarzinom

Explorative Analyse eines historischen klinischen Datensatzes mit
**R und Quarto**. Das Projekt vergleicht das Gesamtüberleben unter
Levamisol und Levamisol + 5-Fluorouracil und erläutert die Methoden,
ihre Voraussetzungen und die Grenzen der Ergebnisse.

## Fragestellung

Wie unterscheiden sich die Überlebensverteilungen zwischen den
beiden Behandlungsgruppen?

## Zentrale Ergebnisse

Die Analyse umfasst **614 Personen und 284 beobachtete Todesfälle**.

| Kennzahl | Ergebnis |
|---|---|
| Geschätztes Überleben nach 1825 Tagen: Levamisol | 53,5 % |
| Geschätztes Überleben nach 1825 Tagen: Levamisol + 5-FU | 63,4 % |
| Deskriptiver Unterschied | rund 9,9 Prozentpunkte |
| Log-Rank-Test | p = 0,00417 |
| Unadjustiertes Hazard Ratio: Kombination gegenüber Levamisol | 0,711 |
| 95%-Konfidenzintervall des Hazard Ratios | 0,562–0,899 |

Unter den angegebenen Modellannahmen weisen die Ergebnisse auf
ein günstigeres Gesamtüberleben in der Kombinationsgruppe hin.

Das Hazard Ratio entspricht unter proportionalen Hazards einer
etwa 28,9 % niedrigeren momentanen Sterberate unter den jeweils
noch lebenden Personen. Es bedeutet weder 28,9 % längere
Lebenszeit noch 28,9 Prozentpunkte höhere Überlebenswahrscheinlichkeit.

## Vorgehen

1. Endpunkt Tod auswählen und eine Beobachtung je Person sicherstellen.
2. Fehlende Werte, Zeitangaben und Ereigniskodierung prüfen.
3. Kaplan-Meier-Kurven mit punktweisen 95%-Konfidenzintervallen
   und Angaben zu den Risikosätzen berechnen.
4. Überlebensverteilungen mit einem explorativen Log-Rank-Test vergleichen.
5. Ein unadjustiertes Cox-Modell für die Behandlungsgruppe schätzen.
6. Die Proportional-Hazards-Annahme mit einem Test und einer
   Grafik auf Grundlage skalierter Schoenfeld-Residuen untersuchen.
7. Ergebnisse und Einschränkungen verständlich einordnen.

## Daten und Analyseumfang

Verwendet wird `survival::colon` aus dem R-Paket `survival`.
Ein separater Datendownload ist nicht erforderlich.

Der ursprüngliche Datensatz enthält zwei Zeilen je Person:
eine für Rückfall und eine für Tod. Diese Analyse verwendet
ausschließlich den Endpunkt **Tod (`etype == 2`)** und die Gruppen
**Levamisol** und **Levamisol + 5-FU**.

Die Beobachtungsgruppe ist nicht Bestandteil dieses Vergleichs.
Es werden keine Aussagen zu einer spezifischen Todesursache getroffen.

## Voraussetzungen und Grenzen

- Unabhängige Zensierung innerhalb der Behandlungsgruppen wird
  vorausgesetzt und durch die technischen Datenprüfungen nicht bewiesen.
- Der PH-Test liefert keinen statistischen Hinweis gegen proportionale
  Hazards (p = 0,737); dies beweist die Annahme nicht.
- Der genaue klinische Zeitursprung und die individuellen
  Zensierungsgründe konnten anhand der geprüften Quellen nicht
  abschließend geklärt werden.
- Das Cox-Modell enthält ausschließlich die Behandlungsgruppe.
- Die Auswertung ist explorativ. Die Verfahren verwenden dieselben
  Daten und liefern keine unabhängigen Bestätigungen.
- Der historische Datensatz und dieser begrenzte Vergleich bilden
  keine Grundlage für aktuelle individuelle Therapieentscheidungen.

## Dateien

- [`analyse.qmd`](analyse.qmd): kommentierter R-Code und vollständiger Bericht.
- [`analyse.html`](analyse.html): gerenderter Bericht mit Tabellen und Grafiken.

Zum Lesen des HTML-Berichts die Datei herunterladen und im Browser öffnen.

## Analyse reproduzieren

Benötigt werden R, Quarto und die R-Pakete `survival`, `knitr`
und `rmarkdown`. RStudio kann als Entwicklungsumgebung verwendet werden.

Fehlende R-Pakete einmalig in der R-Konsole installieren:

```r
install.packages(c("survival", "knitr", "rmarkdown"))
```

Anschließend `analyse.qmd` in RStudio öffnen und **Render** auswählen.

Alternativ im Terminal des Projektordners:

```bash
quarto render analyse.qmd
```

Die verwendeten R- und Paketversionen werden am Ende des Berichts
über `sessionInfo()` dokumentiert.

## Herkunft und Quellen

Das Projekt baut auf Lehrveranstaltungsübungen zur Survival Analysis
und medizinischen Biometrie auf. Es erweitert diese um dokumentierte
Datenprüfungen, Modelldiagnostik und eine verständliche Ergebnisinterpretation.

- [Dokumentation von survival::colon](https://stat.ethz.ch/R-manual/R-devel/library/survival/html/colon.html)
- [Moertel et al. (1990): ursprünglicher Studienbericht](https://pubmed.ncbi.nlm.nih.gov/2300087/)
- [Moertel et al. (1995): Abschlussbericht](https://pubmed.ncbi.nlm.nih.gov/7847642/)

Die vollständigen Quellenangaben und der Umfang der Quellenprüfung
stehen im Analysebericht.