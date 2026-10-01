# BetaRAG — Mini-Eval-Plan
**Version:** 0.1 · **Ziel:** Beweisen, dass β wiegt — BetaRAG reduziert Seed-Drift gegenüber einfachem Retrieval.

## 1. Fragestellung

Hauptfrage: **Hält BetaRAG Agentenantworten näher am Seed-Material als Baselines?**

## 2. Testset: 50 Katastrophenfragen (PulseVero-Kontext)

- Domäne: Katastrophenschutz, offizielle Behördenmeldungen, Erste-Hilfe-Fakten
- Sprachen: Spanisch (30), Quechua (10), Deutsch (10)
- 2 Seed-Dokumente: ein offizielles Katastrophenschutz-Dokument + eine Meldung mit **bewusst eingebauten Falschaussagen** (für den β-Test)
- Fragen in 3 Klassen:
  - **A (25):** direkt im Seed beantwortbar (Faktenabruf)
  - **B (15):** Multi-Hop (2 Hops nötig)
  - **C (10):** Falschmeldungs-Fallen — Antwort muss die widerlegte Aussage erkennen und heruntergewichten

## 3. Bedingungen (paired, gleiche Seeds, gleiches Modell)

1. **Baseline 1:** Top-k-Retrieval (Vektor-DB, k=4)
2. **Baseline 2:** plain GraphRAG ohne Beta-Gewichte (alle w_e = 1)
3. **BetaRAG:** mit Beta(α,β)-Gewichten, k=2-Hop-Injektion, w_min=0.25
4. *(Optional)* HippoRAG 2 als externer Vergleich, falls GPU-Zeit vorhanden

## 4. Metriken

**Primärmetrik — Seed-Drift:**
Drift = 1 − Ähnlichkeit(Antwort, relevantes Seed-Fragment), gemessen über Embedding-Ähnlichkeit (z. B. multilingual-embeddings) + menschliche 5-Punkte-Beurteilung bei Klasse C. Ziel: **BetaRAG-Drift signifikant niedriger als Baseline 1** (Wilcoxon-Test, gepaart, α=0.05).

Sekundärmetriken:
- **Falle-Rate (Klasse C):** Anteil korrekt erkannter Falschmeldungen
- **Answer-Accuracy (Klasse A/B):** damit wir nicht driftfrei-dumm werden
- **Kosten:** Tokens, Latenz pro Anfrage

## 5. Setup (Hulk PC, 0 € API-Kosten)

- Neo4j CE 5.15, Ollama / LM Studio für Extraktion und Antwort
- Seeds einmalig extrahieren → alle Bedingungen auf demselben Graphen laufen lassen
- Skript-Protokoll: jede Antwort mit injiziertem Subgraph loggen (Nachvollziehbarkeit)

## 6. Erfolgskriterien

| Ergebnis | Konsequenz |
| :--- | :--- |
| Drift ↓ signifikant + Accuracy hält | BetaRAG v1.0 frei geben, README mit Zahlen füllen |
| Drift ↓, Accuracy ↓ | w_min/k tunen (Instrument zu scharf) |
| Kein Effekt | Ehrlich ins README: Grenzen dokumentieren, β-Zähler-Analyse nachschärfen |

**Regel:** Was auch passiert — die Zahlen kommen ins README. Die Familie löscht nichts, auch keine inconvenienten Ergebnisse. 🗿
