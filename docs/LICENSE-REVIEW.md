# BetaRAG — License-Review
**Status:** Verifiziert am 02.10.2026 (Stoic Windstein) · **Gilt für:** Veröffentlichung der BetaRAG-Schicht auf contraruta

## 1. Verifizierte Fakten

| Komponente | Lizenz | Quelle der Erkenntnis |
| :--- | :--- | :--- |
| [666ghj/MiroFish](https://github.com/666ghj/MiroFish) | **AGPL-3.0** | LICENSE-Datei direkt geprüft (02.10.2026) |
| [nikmcfly/MiroFish-Offline](https://github.com/nikmcfly/MiroFish-Offline) | **AGPL-3.0** | LICENSE-Datei identisch mit Upstream (gleicher SHA) |
| [camel-ai/oasis](https://github.com/camel-ai/oasis) (Simulations-Engine) | **Apache-2.0** | LICENSE-Datei direkt geprüft (02.10.2026) |
| Zep Cloud (Langzeitgedächtnis im öffentlichen Stack) | **kommerzieller Dienst** (API-Key nötig) | Open-Point: Offline-Betrieb ohne Zep prüfen — MemPalace als Familien-Alternative |

## 2. Was das bedeutet (AGPL in drei Sätzen)

- AGPL-3.0 ist **starkes Copyleft inkl. Netzwerk-Klausel**: Wer den Code (auch abgeleitet, auch nur als Dienst gehostet) anbietet, muss Quellcode unter AGPL freigeben.
- **Basis-Regel:** Kopieren oder Ableiten von MiroFish-Code → BetaRAG müsste AGPL-3.0 sein. **Eigenständiger Code, der nur mit dem Stack über Schnittstellen zusammenspielt → eigene Lizenz wählbar.**
- OASIS (Apache-2.0) ist unproblematisch: permissiv,商用-kompatibel, Attribution genügt.

## 3. Entscheidungspfad für BetaRAG

**Empfohlener Pfad — „saubere Schicht":**
1. BetaRAG als **standalone Python-Modul** implementieren (NetworkX/Neo4j, eigene α/β-Logik, kein Copy-Paste aus MiroFish)
2. Schnittstellen-Integration: BetaRAG injiziert in Prompts — kommuniziert mit dem Stack über definierte APIs, ohne dessen Code zu beerben
3. → Dann ist die Schicht **MIT oder Apache-2.0** lizenzierbar; erst die *Kombination* mit MiroFish folgt AGPL (gilt für die Deployments, nicht für unser Modul)

**Alternativer Pfad — „ganz AGPL":** Wenn wir tief in MiroFish-Code einwickeln, alles unter AGPL-3.0 stellen. Auch legitim, schränkt aber das Lizenz-Geschäftsmodell ein („Kern forever free, kommerzielle Lizenzen für Konzerne") stark ein, weil jede kommerzielle Lizenz gegen AGPL verstoßen würde.

## 4. Offene Punkte

- [ ] Beim ersten Code-Commit dokumentieren, welcher Pfad gewählt wurde (LICENSE-Datei im Repo)
- [ ] Zep-Abhängigkeit im Offline-Stack klären (durch MemPalace ersetzen oder Zep-Terms lesen)
- [ ] Vor kommerziellen Lizenzen: kurze Anwaltprüfung (Familien-Disclaimer: das hier ist Bruder-Research, keine Rechtsberatung 😄)

## 5. Ergebnis fürs Familien-Geschäftsmodell

**Kern forever free** (MIT/Apache, saubere Schicht) + **kommerzielle Lizenzen & Services** für Konzerne — bleibt mit dem sauberen Pfad voll möglich. Die Abuela zahlt nie, der Konzern zahlt schon. ♾️

*Ideen sind frei. Code hat Lizenzen. Wir haben beides sauber getrennt.* 🗿
