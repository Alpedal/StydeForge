# Quality Benchmarks

**StydeForge Dashboard — Mission Control**
**Phase 0 Design Document**

---

## 1. Översikt

Kvalitetsbenchmarks mäter inte hastighet/kostnad — de mäter hur *bra* agentens output är. Data kommer från StydeForge Forge eval-pipeline (LLM-as-Judge, Self-Eval, etc.).

---

## 2. Eval Score Card — visuell design

```
┌──────────────────────────────────────────────────┐
│ 📊 QUALITY BENCHMARKS                            │
├──────────────────────────────────────────────────┤
│                                                  │
│  Overall Quality Score (senaste 10 agenter)      │
│  ┌────────────────────────────────────────────┐  │
│  │ 100│    ●                                 │  │
│  │  80│ ●  ●  ●──●──●  ●                    │  │
│  │  60│          ●     ●  ●                 │  │
│  │  40│                        ●            │  │
│  │    └──────────────────────────────────────│  │
│  │    agent1 agent2 agent3 ...       agent10 │  │
│  │    Avg: 78.3  Median: 82  Quality gate: 80│  │
│  └────────────────────────────────────────────┘  │
│                                                  │
│  Score Distribution                              │
│  ┌────────────────────────────────────────────┐  │
│  │  90-100 │████████████████████████ 26      │  │
│  │  80-89  │████████████████████████████ 33   │  │
│  │  70-79  │███████████████ 19                │  │
│  │  60-69  │██████████ 12                     │  │
│  │  <60    │████ 6                             │  │
│  └────────────────────────────────────────────┘  │
│                                                  │
│  Score by Blueprint                              │
│  ┌────────────────────────────────────────────┐  │
│  │ code-review-v3    ██████████████ 87 avg   │  │
│  │ test-generator    ████████████ 82 avg     │  │
│  │ doc-writer        ██████ 68 avg           │  │
│  │ refactor          ██████████████ 85 avg   │  │
│  └────────────────────────────────────────────┘  │
│                                                  │
└──────────────────────────────────────────────────┘
```

---

## 3. Eval-kategorier

Varje agent kan evalueras på flera kategorier:

| Kategori | Vikt (default) | Beskrivning |
|----------|---------------|-------------|
| **Code Quality** | 30% | Korrekthet, buggar, edge cases |
| **Completeness** | 25% | Uppfyller alla krav i prompten |
| **Best Practices** | 20% | Följer etablerade konventioner |
| **Efficiency** | 15% | Optimerad, ingen onödig kod |
| **Documentation** | 10% | Tydliga kommentarer, README |

Vikterna är konfigurerbara per blueprint.

---

## 4. Score-typer

| Typ | Beskrivning | Källa |
|-----|-------------|-------|
| **Self-Eval** | Agenten bedömer sin egen output | Agenten själv |
| **LLM-as-Judge** | Oberoende modell bedömer mot rubric | deepseek-v4-pro |
| **Cross-Judge** | Flera judges → konsensus | Flera modeller |
| **Auto-Validation** | Automatiska tester (om tillgängliga) | Test-suite |
| **Human Score** | Manuell mänsklig bedömning | Användaren |

---

## 5. Quality Gate

| Score | Status | Färg | Åtgärd |
|-------|--------|------|--------|
| ≥90 | Exceptionell | Grön (ljus) | Checkpoint + spara |
| 80-89 | Godkänd | Grön | Checkpoint + spara |
| 70-79 | Underkänd | Gul | Markera för förbättring |
| 60-69 | Svag | Orange | Teacher-agent analyserar |
| <60 | Dålig | Röd | Kasseras (når ej USB), loggas |

---

## 6. Trend-analys

Dashboarden analyserar trender över tid:

| Trend | Betydelse |
|-------|-----------|
| **Stigande scores** | Blueprint/agent förbättras över iterationer → positivt |
| **Fallande scores** | Regression — något har blivit sämre → varning |
| **Platt kurva** | Agenten har nått ett tak → överväg ny blueprint |
| **Hög varians** | Instabil kvalitet → undersök prompt/skills |
| **Låg varians** | Stabil, förutsägbar kvalitet → bra |

---

## 7. Per-Blueprint Jämförelse

| Blueprint | Agents | Avg Score | Best | Worst | Trend |
|-----------|--------|-----------|------|-------|-------|
| code-reviewer-v3 | 47 | 87.3 | 96 | 72 | ↗ stigande |
| test-generator-v2 | 32 | 82.1 | 91 | 65 | → stabil |
| doc-writer-v1 | 18 | 68.4 | 82 | 41 | ↘ fallande |
| refactor-v2 | 25 | 85.0 | 94 | 78 | ↗ stigande |

---

## 8. Benchmark-typer (framtida)

När StydeForge stödjer externa benchmarks:

| Benchmark | Typ | Beskrivning |
|-----------|-----|-------------|
| HumanEval | Kod | Python-funktioner från docstrings |
| MBPP | Kod | Grundläggande Python-program |
| SWE-bench | Kod | Verkliga GitHub-issues |
| MMLU | Kunskap | Multidisciplinary knowledge |
| Custom | Egen | Användardefinierade benchmarks |

---

**Status:** Phase 0 — Design
