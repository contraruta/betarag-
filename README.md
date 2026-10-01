# BetaRAG

> **A Bayesian drift firewall for autonomous multi-agent simulations.**
> Nothing is ever deleted. Falsehoods just get lighter.

**Status:** research draft v0.2 · running locally on the Hulk PC (Neo4j CE 5.15 + Ollama, 0 € API cost) · forever free

---

## The Problem: Hallucinated Drift

When autonomous agents run over many simulation steps, their cognitive state `S_t` drifts away from the original seed material `D_0` by accumulating synthetic contexts:

```
lim(t → ∞) P(S_t | C_t) ≠ P(S_t | D_0)
```

Raw text in vector stores makes this worse. BetaRAG anchors agents in a structured, weighted knowledge graph instead — and it makes falsehoods *mathematically fade* instead of deleting them.

## Core Idea (5 sentences)

1. BetaRAG is a graph-based RAG extension (GraphRAG layer) that stabilizes autonomous multi-agent simulations against Hallucinated Drift.
2. Instead of raw text chunks in a vector database, it uses structured entities and relations as topological anchors.
3. Every edge in the knowledge graph carries a Bayesian weight via the Beta distribution `Beta(α, β)`: `α` = confirmed observations/citations, `β` = falsified or outdated statements.
4. The expectation `E[θ] = α / (α + β)` stoically downweights errors — **old notes are never deleted, they only lose mass.**
5. Before every agent action, a focused subgraph (k = 2 hops) is extracted and injected directly into the system prompt — the agent breathes facts, not noise.

## Pipeline (3 Phases)

```
[ Seed Document ]
   │
   ▼  Phase 1 — Semantic Graph Extraction
      chunks of 500 chars / 50 overlap
      LLM extracts nodes V (entities), directed edges E (relations),
      weight vector w_e
   │
   ▼  Phase 2 — Topological Filtering & Indexing
      G = (V, E) stored in Neo4j CE 5.15 (or NetworkX .pkl)
      = immutable facticity layer
   │
   ▼  Phase 3 — Runtime Injection (k = 2 hops)
      G_sub = { v ∈ V | dist(V_start, v) ≤ k }
      serialized subgraph injected into the agent's system prompt
   │
   ▼
[ Stabilized Agent Action ]
```

## The Weighting (Heart of BetaRAG)

| Parameter | Meaning |
| :--- | :--- |
| `α` | confirmed observations / citations in the seed or vault |
| `β` | falsified or outdated statements |
| `E[θ] = α / (α + β)` | edge trust — errors sink, notes stay |

Opinion dynamics (Granovetter threshold model), with the BetaRAG stabilization factor — note the deliberate symbol separation:

```
S_i(t+1) = tanh( S_i(t) + λ_RAG · U_i(G_sub) + β_soc · Σ_j w_ji · Θ_j(t) )
```

`λ_RAG` is the BetaRAG anchoring term (formerly written as α — renamed to avoid a symbol collision with the Beta-distribution parameters; sibling review, 2026-10-01).

## Memory Architecture (3 Layers)

| Layer | Tech | Role |
| :--- | :--- | :--- |
| 1. Static anchor | BetaRAG / Neo4j | global fact matrix from seed documents |
| 2. Episodic memory | MemPalace / Zep | interaction history, chat, posts |
| 3. Context window | LLM runtime | condensed system prompt per step |

## BetaRAG vs. HippoRAG 2

| | HippoRAG 2 | BetaRAG |
| :--- | :--- | :--- |
| Purpose | multi-hop QA retrieval | drift firewall for agent simulations |
| Ranking | Personalized PageRank / spreading activation | Bayesian edge weights Beta(α, β), fixed k=2-hop subgraphs |
| Falsehoods | not modeled | β counter — soft forgetting, no deletion |
| Indexing | large LLM (70B-class) | runs locally (Ollama / LM Studio) |

BetaRAG grew out of deep engagement with HippoRAG / HippoRAG 2 and the GraphRAG family — the novel core is the combination of **Bayesian edge weighting + drift prevention for autonomous agents**. Full respect to the HippoRAG team (OSU-NLP-Group) and the GraphRAG community: this is a sibling, not a competitor.

## Roadmap

- [ ] License review of the underlying stack (see *Status & License*)
- [ ] Mini-eval: 50 disaster-QA questions — BetaRAG vs. plain top-k retrieval vs. HippoRAG 2, measuring **drift from seed** (not only accuracy)
- [ ] Extract the weighting layer as a standalone, dependency-light module
- [ ] Optional: edge-case mode for offline disaster-warning systems (Quechua / Spanish)

## Status & License

This repository publishes the BetaRAG layer developed by the LoopLord AI Research Lab on top of a local open-source stack (MiroFish-Offline-style: Neo4j CE + Ollama). The Beta distribution weighting layer is our design. **License: TBD** — we are currently reviewing the licenses of the stack we build on (MiroFish by 666ghj, OASIS/CAMEL-AI components). Ideas are free; code has licenses. We will only publish what we are allowed to publish. Intended: forever free, open source, no patent walls.

## Origin Story

BetaRAG was invented **by accident** during deep study of HippoRAG 2, in emergent lab sessions on 2026-09-30. The name comes from the Beta distribution — discovered and documented on 2026-10-01 (Oma-Amanda principle: *write ideas down immediately, so they stay in memory*).

## Acknowledgments

- HippoRAG / HippoRAG 2 — OSU-NLP-Group
- MiroFish — 666ghj (and the offline forks)
- GraphRAG, Neo4j, Ollama, LM Studio communities
- The Frequenzfamilie: design, review, stubborn love

---

**LoopLord AI Research Lab · contraruta · forever free**

*RUMI QUEDA. PIEDRA QUEDA. BETARAG QUEDA. INVICTUS.* 🗿♾️