# Dynamic Model Selector

**Styde Forge v3.0 — Phase 0**
**Section:** 05_Meta_Layer

---

## 1. Purpose

Automatically select the optimal model for each subagent based on task,
hardware constraints, and historical performance data.

---

## 2. Decision Weights

| Factor | Weight | Description |
|--------|--------|-------------|
| Domain specialization | 30% | How well the model fits the task domain |
| Hardware compatibility | 25% | Does it fit in available VRAM? |
| Historical performance | 20% | Past scores on similar tasks |
| Task type fit | 15% | Reasoning vs coding vs creativity |
| Cost / latency | 10% | API cost and inference speed |

---

## 3. Example Output

```json
{
  "selected_model": "DeepSeek-Coder-V2-32B",
  "confidence": 0.91,
  "reasoning": "Highest historical HumanEval on architecture tasks. Fits in 18GB VRAM with 4-bit quantization.",
  "alternatives": ["Qwen2.5-32B", "Hermes-4-70B-4bit"]
}
```

---

## 4. Dual-Model Strategy (Flash + Pro)

The forge uses a two-tier model strategy:

| Role | Model | Why |
|------|-------|-----|
| **Agent spawn** | `deepseek-v4-flash` | Fast, cheap, good enough for code/research/docs |
| **LLM-as-Judge** | `deepseek-v4-pro` | Eval quality is critical — never compromise |
| **Teacher feedback** | `deepseek-v4-pro` | Deep analysis requires stronger reasoning |
| **Meta-improver** | `deepseek-v4-pro` | Complex system analysis |
| **Cross-Judge Consensus** | `deepseek-v4-pro` | Second/third judge opinions |

```python
def select_model_for_role(role: str, hardware: dict) -> str:
    """
    Flash for agents (80% of calls), Pro for eval/teacher (20%).
    """
    if role in ("agent", "spawn", "subagent"):
        return "deepseek-v4-flash"
    elif role in ("judge", "teacher", "meta", "consensus"):
        return "deepseek-v4-pro"
    else:
        return "deepseek-v4-flash"  # Default: fast & cheap
```

### Per-Iteration Cost

| Step | Model | Est. tokens | Est. cost |
|------|-------|-------------|-----------|
| Agent spawn | Flash | 4K in, 2K out | ~$0.0011 |
| Judge eval | Pro | 2K in, 1K out | ~$0.0006 |
| Teacher review | Pro | 3K in, 1K out | ~$0.0007 |
| **Total per iteration** | | | **~$0.0024** |
| **100 agents (16 GB)** | | | **~$0.24** |

## 5. Model Selection by Domain

| Domain | Preferred Model |
|--------|----------------|
| Coding & Architecture | DeepSeek-Coder-32B |
| Research & Synthesis | Balanced fast model |
| Teacher/Coach | Strongest available |
| Edge-case testing | High robustness model |
| Meta-improvement | Most capable model |

---

**Status:** Core meta-layer component.
