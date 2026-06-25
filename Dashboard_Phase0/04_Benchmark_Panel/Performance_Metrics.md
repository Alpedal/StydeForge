# Performance Metrics

**StydeForge Dashboard — Mission Control**
**Phase 0 Design Document**

---

## 1. Översikt

Benchmark-panelen visar prestandamätningar över tid: tokens per sekund, latency, och kostnad — per agent, per modell, och aggregerat.

---

## 2. Live Metrics — visuell design

```
┌──────────────────────────────────────────────────┐
│ 📊 BENCHMARKS                        [24h ▼]    │
├──────────────────────────────────────────────────┤
│                                                  │
│  Tokens per Second (live)                        │
│  ┌────────────────────────────────────────────┐  │
│  │ 60│                   ╭─╮                  │  │
│  │ 40│     ╭──╮   ╭─────╯ ╰──╮  ╭─           │  │
│  │ 20│╭───╯  ╰───╯          ╰──╯             │  │
│  │  0│└────────────────────────────────────── │  │
│  │   15:30    15:35     15:40     15:45      │  │
│  │   Avg: 42 t/s  Peak: 68 t/s  Now: 45 t/s │  │
│  └────────────────────────────────────────────┘  │
│                                                  │
│  ┌──────────────┬──────────────┬──────────────┐  │
│  │  Latency     │  Cost/hr     │  Agents/hr   │  │
│  │              │              │              │  │
│  │   1.2s       │   $0.042     │    4.2       │  │
│  │   avg resp   │   running    │   completed  │  │
│  │   ↓ 0.3s     │   ↑ $0.005   │   ↑ 1.1      │  │
│  └──────────────┴──────────────┴──────────────┘  │
│                                                  │
│  Per-Model Performance                           │
│  ┌────────────────────────────────────────────┐  │
│  │ Model              t/s    Lat    Cost/1K  │  │
│  │ ───────────────────────────────────────────┤  │
│  │ deepseek-v4-flash  52    0.8s   $0.00027  │  │
│  │ deepseek-v4-pro    28    2.1s   $0.00110  │  │
│  │ gpt-4o             35    1.5s   $0.00500  │  │
│  └────────────────────────────────────────────┘  │
│                                                  │
└──────────────────────────────────────────────────┘
```

---

## 3. Metriker

### 3.1 Primära metriker

| Metrik | Enhet | Beskrivning | Datakälla |
|--------|-------|-------------|-----------|
| **Tokens/s** | tokens/sekund | Genomströmning | Hermes logg (token counter) |
| **Latency** | millisekunder | Tid till första token (TTFT) | Provider API |
| **Cost/token** | USD | Kostnad per 1000 tokens | Provider-prislista |
| **Cost/hr** | USD | Total kostnad per timme | Aggregerat från alla agenter |
| **Agents/hr** | antal | Slutförda agenter per timme | Agent-historik |

### 3.2 Sekundära metriker

| Metrik | Beskrivning |
|--------|-------------|
| **Queue depth** | Antal agenter i kö |
| **Avg agent duration** | Genomsnittlig tid per agent |
| **Success rate** | % agenter som klarar quality gate (≥80) |
| **Token efficiency** | Output tokens / input tokens (bör vara högt) |
| **Error rate** | % agenter som felar |

---

## 4. Tidsintervall

| Intervall | Upplösning | Beskrivning |
|-----------|------------|-------------|
| Live (5 min) | Per sekund | Realtid — senaste 5 minuterna |
| 1 hour | Per minut | Senaste timmen |
| 24 hours | Per 5 minuter | Senaste dygnet |
| 7 days | Per timme | Senaste veckan |
| 30 days | Per dag | Senaste månaden |

---

## 5. Per-Model Jämförelse

Tabell som jämför alla modeller:

| Kolumn | Beskrivning |
|--------|-------------|
| Model | Modellnamn |
| Provider | Provider (DeepSeek, OpenAI, etc.) |
| t/s (avg) | Genomsnittlig tokens/sekund |
| t/s (peak) | Högsta uppmätta tokens/sekund |
| Latency (avg) | Genomsnittlig TTFT |
| Latency (p95) | 95:e percentilen latency |
| Cost/1K tokens | Kostnad per 1000 tokens |
| Agents run | Antal agenter körda med denna modell |
| Avg Score | Genomsnittlig eval-score för denna modell |

---

## 6. Grafer — specifikation

### 6.1 Tokens per Second (line chart)

- X-axel: tid
- Y-axel: tokens/s
- En linje per modell (färgkodad)
- Hover: exakt värde + tidpunkt
- Annoteringar: markera när en ny agent spawnades

### 6.2 Cost Over Time (stacked area)

- X-axel: tid
- Y-axel: kostnad (USD)
- Stackad area per modell
- Kumulativ total som en separat linje

### 6.3 Agent Throughput (bar chart)

- X-axel: tidsbuckets (per timme)
- Y-axel: antal slutförda agenter
- Staplar: grön (score ≥80), gul (60-79), röd (<60)

### 6.4 Latency Distribution (histogram)

- X-axel: latency buckets (0-500ms, 500ms-1s, 1-2s, 2-5s, 5s+)
- Y-axel: antal requests
- Per modell

---

## 7. Datalagring

Prestandadata sparas lokalt:

```json
{
  "timestamp": "2026-06-25T15:42:00Z",
  "agent_id": "ag-xyz-123",
  "model": "deepseek-v4-flash",
  "provider": "deepseek",
  "metrics": {
    "tokens_in": 4200,
    "tokens_out": 2642,
    "total_tokens": 6842,
    "duration_ms": 225000,
    "ttft_ms": 850,
    "cost_usd": 0.019,
    "tokens_per_second": 30.4
  }
}
```

**Lagringsstrategi:**
- Rådata: IndexedDB — senaste 30 dagarna
- Aggregerad data: per timme, dag, vecka — komprimerad
- Rensning: äldre än 30 dagar → aggregat sparas, rådata raderas

---

**Status:** Phase 0 — Design
