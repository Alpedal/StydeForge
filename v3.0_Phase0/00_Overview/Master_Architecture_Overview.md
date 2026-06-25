# Master Architecture Overview

**Styde Forge v3.0 — The Crucible**
**Phase 0 Design Document**

---

## 1. Vision

Styde Forge v3.0 is a **portable, evolutionary elite-agent refinery** on USB.

It transforms raw agent blueprints into world-class specialized agents through
a continuous loop of spawning, evaluation, improvement, and checkpointing.

**Not a content factory — a refinery.** Quality over quantity. Nothing below
80/100 reaches the USB.

---

## 2. Architecture Layers

```
┌─────────────────────────────────────────────────────────────┐
│                    PARENT HERMES                             │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │              META-LAYER                              │   │
│  │  ┌──────────────┐ ┌──────────────┐ ┌─────────────┐  │   │
│  │  │ Model        │ │ Historical   │ │ Version     │  │   │
│  │  │ Selector     │ │ Learning     │ │ Increment   │  │   │
│  │  └──────────────┘ └──────────────┘ └─────────────┘  │   │
│  │  ┌──────────────┐ ┌──────────────┐                  │   │
│  │  │ Self-        │ │ Hardware     │                  │   │
│  │  │ Monitoring   │ │ Adaptation   │                  │   │
│  │  └──────────────┘ └──────────────┘                  │   │
│  └─────────────────────────────────────────────────────┘   │
│                            │                                │
│                    ┌───────┴───────┐                        │
│                    │   SUBAGENT    │                        │
│                    │   SPAWNING    │                        │
│                    └───────┬───────┘                        │
│                            │                                │
│  ┌─────────────────────────────────────────────────────┐   │
│  │               EVAL PIPELINE                          │   │
│  │  Self-Eval → LLM-as-Judge → Cross-Consensus         │   │
│  │           → Bias Calibration → Bayesian Opt         │   │
│  └─────────────────────────────────────────────────────┘   │
│                            │                                │
│  ┌─────────────────────────────────────────────────────┐   │
│  │            PERSISTENCE & SAFETY                      │   │
│  │  Atomic Transactions → Checkpoints → Recovery       │   │
│  └─────────────────────────────────────────────────────┘   │
│                            │                                │
│                     ┌──────┴──────┐                         │
│                     │     USB     │                         │
│                     │ PERSISTENCE │                         │
│                     └─────────────┘                         │
└─────────────────────────────────────────────────────────────┘
```

---

## 3. Core Loop

```
DEFINE ──→ SPAWN ──→ EVALUATE ──→ IMPROVE ──→ CHECKPOINT
   ↑                                               │
   └───────────────────────────────────────────────┘
```

### 3.1 Define
Create or refine an agent blueprint (persona, skills, config, hardware profile).

### 3.2 Spawn
Launch subagent via `delegate_task` with:
- Dynamically selected model
- Domain-specific skills
- Historical context and lessons

### 3.3 Evaluate
Six-layer evaluation:
1. **Self-Eval** — agent evaluates its own output
2. **LLM-as-Judge** — independent model judges against rubric
3. **Cross-Judge Consensus** — multiple judges compared
4. **Bias Calibration** — periodic calibration against known benchmarks
5. **Automatic Validation** — automated tests + benchmarks
6. **Bayesian Weight Optimization** — adaptive weight updates

### 3.4 Improve
Teacher/coach analyzes eval results and:
- Extracts successful patterns as new skills
- Identifies recurring weaknesses
- Proposes concrete blueprint improvements
- Updates version via Automatic Version Increment

### 3.5 Checkpoint
Atomic snapshot of entire forge state:
- Full state.yaml
- All blueprints with version history
- Agent run history
- Eval results

---

## 4. Hardware Adaptivity

| Parameter | Machine-A (Beast) | Machine-B (Main) |
|-----------|-------------------|------------------|
| GPUs | 3090 (24GB) + 3080 (10GB) | 3080 (10GB) + 3070 Ti (8GB) |
| Total VRAM | 34 GB | 18 GB |
| RAM | 64 GB | 32 GB |
| Sampling | NUTS (depth 11) | VI (depth 8) |
| Workers | 4 | 1-2 |
| Models | 70B-405B | 7B-14B |

---

## 5. Design Principles

| Principle | Meaning |
|-----------|---------|
| **One logical home** | Every piece of data has exactly one canonical location |
| **Atomicity first** | All writes are transactional — never partial |
| **Hardware aware** | System auto-adapts to available resources |
| **Full traceability** | Every decision, eval, and version change is logged |
| **Quality gate** | Nothing below 80/100 is saved |
| **Self-contained** | The USB is the entire system — no external dependencies |

---

**Status:** Phase 0 — Design & Foundation

---

## Related Documents

- `01_Vision/Vision_and_Goals.md` — What we're building and why
- `USB_Directory_Structure.md` — Where everything lives on USB
- `Data_Models.md` — All YAML/JSON schemas
- `Core_Loop_Detail.md` — Exact loop specification
- `Component_Interfaces.md` — How components communicate
- `03_Eval_Pipeline/` — 7 evaluation documents
- `04_Sampling_Stack/` — 5 Bayesian sampling documents
- `05_Meta_Layer/` — 4 meta-layer documents
- `06_Persistence_Safety/` — 4 safety documents
