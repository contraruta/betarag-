# BetaRAG — Technical Specification
**Version:** 0.2 · **Status:** Draft · **Repo:** contraruta · **Stack-Basis:** [MiroFish](https://github.com/666ghj/MiroFish) (AGPL-3.0, siehe [LICENSE-REVIEW.md](LICENSE-REVIEW.md))

BetaRAG ist eine Bayesianische GraphRAG-Schicht zur Stabilisierung autonomer Multi-Agenten-Simulationen. Sie verhindert den *Hallucinated Drift*: die schleichende Entfernung des kognitiven Zustands S_t vom Seed-Material D₀ über fortlaufende Simulationsrunden.

## 1. Zentrale Invarianten

1. **Nichts wird gelöscht.** Falsifizierte Aussagen bleiben gespeichert, verlieren nur Gewicht.
2. **Gewichte sind Bayesianisch.** Jede Kante trägt einen α-Zähler (bestätigt) und β-Zähler (falsifiziert/veraltet).
3. **Injektion statt Suche.** Vor jeder Agenten-Aktion wird ein Subgraph in den Prompt injiziert — kein freies Retrieval.

## 2. Datenmodell

```
Knoten v ∈ V        Entität, mit Typ (Person, Ort, Ereignis, Behauptung, Quelle)
Kante e = (u, v) ∈ E  gerichtete Relation mit Label r_e
Zustand pro Kante:  α_e ∈ ℕ (Konfirmationen), β_e ∈ ℕ (Falsifikationen)
Gewicht:            w_e = E[θ_e] = α_e / (α_e + β_e)
Prior:              α_e, β_e starten bei 1 (Laplace) — nie bei 0, sonst	dividiert das Nichtwissen durch sich selbst.
```

Update-Regeln:
- **Konfirmation:** Beobachtung/Zitat validiert die Relation → α_e += 1
- **Falsifikation:** Widerspruch mit Quelle → β_e += 1
- **Veraltung:** Aussage trägt Timestamp; nach Ablauf der Gültigkeit γ_decay (per Entitätstyp konfigurierbar) → β_e += 1, ohne Löschung

## 3. Pipeline

**Phase 1 — Semantische Graphen-Extraktion (offline):**
- Chunking: 500 Zeichen, 50 Overlap
- LLM extrahiert V, E, Labels, Quellenverweis pro Kante
- Jede Kante startet mit α=1, β=1 (Laplace-Prior)

**Phase 2 — Topologische Filterung & Indexierung (offline):**
- Ablage: Neo4j CE 5.15 (Empfehlung für den Hulk PC) oder NetworkX-Serialisierung (.pkl) für Edge-Einsatz (Panchita IA)
- Unveränderliche Faktizitätsebene: keine Schreibzugriffe zur Laufzeit, nur Zähler-Updates über definierte API

**Phase 3 — Runtime-Injektion (pro Agenten-Aktion):**
- Subgraph-Extraktion: G_sub = {v ∈ V | dist(V_start, v) ≤ k}, Default k=2
- Serialisierung mit Kantengewichten: nur Kanten mit w_e ≥ w_min (Default 0.25) werden injiziert — Rauschen bleibt außen vor
- Injektion in den System-Prompt des Agenten

## 4. Meinungs-Dynamik (Granovetter-Schwellenwertmodell)

S_i(t+1) = tanh( S_i(t) + λ_RAG · U_i(G_sub) + γ · Σ_{j∈N_i} w_ji · Θ_j(t) )

- **λ_RAG** — BetaRAG-Stabilisierungsfaktor (Stärke der Subgraph-Verankerung)
- **γ** — soziale Kopplung (Nachbarschaftseinfluss)
- **U_i(G_sub)** — Nutzen/Relevanz des injizierten Subgraphen für Agent i

⚠️ **Symbol-Konvention (verbindlich ab v0.2):** λ_RAG und γ NICHT α/β nennen — die sind für die Beta-Verteilung reserviert (Konfirmation/Falsifikation). Namenskollisionen sind Reviews-Killer.

## 5. Konfiguration (Defaults)

| Parameter | Default | Bedeutung |
| :--- | :--- | :--- |
| chunk_size | 500 | Zeichen pro Chunk |
| chunk_overlap | 50 | Überlappung |
| k_hops | 2 | Subgraph-Radius |
| w_min | 0.25 | Mindest-Kantengewicht für Injektion |
| alpha_prior | 1 | Laplace-Prior α |
| beta_prior | 1 | Laplace-Prior β |
| gamma_decay | typabhängig | Veraltungsfenster |

## 6. Nächste Schritte

1. Referenz-Implementierung als **standalone Python-Modul** (NetworkX-first, Neo4x-Adapter optional) — siehe Lizenz-Pfadberechnung in LICENSE-REVIEW.md
2. Mini-Eval gemäß [EVAL-PLAN.md](EVAL-PLAN.md)
3. Zahlen beibringen: α/β-Zähler visualisieren

**Forever free. Nichts wird gelöscht.** 🗿♾️
