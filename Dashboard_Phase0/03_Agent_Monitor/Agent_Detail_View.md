# Agent Detail View

**StydeForge Dashboard — Mission Control**
**Phase 0 Design Document**

---

## 1. Översikt

Klick på en agent i listan öppnar en detaljvy — antingen som en slide-in panel från höger eller som en expanderad sektion i agent-listan.

---

## 2. Detaljvy — layout

```
┌──────────────────────────────────────────────────┐
│ ← Back to Agents                                 │
├──────────────────────────────────────────────────┤
│                                                  │
│  ● code-reviewer-v3                     87/100  │
│  ─────────────────────────────────────────────  │
│                                                  │
│  ┌─ Overview ──────────────────────────────────┐ │
│  │ Status:     Completed                        │ │
│  │ Model:      deepseek-v4-flash                │ │
│  │ Blueprint:  code-reviewer-v3                 │ │
│  │ Duration:   3m 45s                           │ │
│  │ Tokens:     6,842 (4.2K in / 2.6K out)      │ │
│  │ Cost:       $0.019                           │ │
│  │ Speed:      32 t/s avg                       │ │
│  │ Started:    2026-06-25 15:38:12              │ │
│  │ Finished:   2026-06-25 15:41:57              │ │
│  └──────────────────────────────────────────────┘ │
│                                                  │
│  ┌─ Evaluation ────────────────────────────────┐ │
│  │ Overall Score: 87/100                        │ │
│  │                                              │ │
│  │ Category         Score   Weight              │ │
│  │ ────────────────────────────────────         │ │
│  │ Code Quality     91/100  30%                │ │
│  │ Completeness     85/100  25%                │ │
│  │ Best Practices   88/100  20%                │ │
│  │ Efficiency       82/100  15%                │ │
│  │ Documentation    90/100  10%                │ │
│  │                                              │ │
│  │ Judge: deepseek-v4-pro                       │ │
│  │ Eval time: 0.8s                              │ │
│  └──────────────────────────────────────────────┘ │
│                                                  │
│  ┌─ Output ────────────────────────────────────┐ │
│  │ [Preview] [Raw] [Download]                   │ │
│  │ ┌──────────────────────────────────────────┐ │ │
│  │ │ 1│# Code Review: auth-service.ts         │ │ │
│  │ │ 2│                                       │ │ │
│  │ │ 3│## Summary                            │ │ │
│  │ │ 4│The authentication service is well-    │ │ │
│  │ │ 5│structured but has 3 issues:            │ │ │
│  │ │...                                       │ │ │
│  │ └──────────────────────────────────────────┘ │ │
│  └──────────────────────────────────────────────┘ │
│                                                  │
│  ┌─ Log ───────────────────────────────────────┐ │
│  │ [All] [Errors] [Warnings] [Info]             │ │
│  │ ┌──────────────────────────────────────────┐ │ │
│  │ │15:38:12 [INFO]  Agent spawned            │ │ │
│  │ │15:38:12 [INFO]  Loading skills...        │ │ │
│  │ │15:38:13 [INFO]  Skills loaded: code-     │ │ │
│  │ │15:38:13 [INFO]  Starting task...         │ │ │
│  │ │15:38:45 [WARN]  File too large, chunking │ │ │
│  │ │...                                       │ │ │
│  │ └──────────────────────────────────────────┘ │ │
│  └──────────────────────────────────────────────┘ │
│                                                  │
│  [🔄 Retry] [📋 Copy Output] [💾 Export] [✕]    │
│                                                  │
└──────────────────────────────────────────────────┘
```

---

## 3. Sektion: Overview

| Fält | Beskrivning |
|------|-------------|
| Status | ● Running / ✓ Completed / ✗ Failed |
| Model | Modellnamn + provider |
| Blueprint | Blueprint som agenten spawnades från |
| Duration | Total körtid |
| Tokens | Input + output tokens |
| Cost | Uppskattad API-kostnad |
| Speed | Genomsnittlig tokens/sekund |
| Started | Start-tid (ISO 8601) |
| Finished | Slut-tid (eller "—" om running) |
| Skills | Lista av skills som laddades |

---

## 4. Sektion: Evaluation

Visas endast om agenten har slutförts och evaluerats.

| Element | Beskrivning |
|---------|-------------|
| Overall Score | 0-100, färgkodat (grön ≥80, gul ≥60, röd <60) |
| Kategorier | Tabell med delpoäng och vikter |
| Judge | Vilken modell som agerade domare |
| Eval time | Hur lång tid evalueringen tog |
| Feedback | Kvalitativ feedback från judge (om tillgänglig) |

---

## 5. Sektion: Output

| Vy | Beskrivning |
|----|-------------|
| Preview | Renderad markdown/syntax-highlight |
| Raw | Rå text |
| Download | Ladda ner som .md / .txt / .json |
| Diff | Om agenten modifierade filer — visa diff (unified diff-format) |

---

## 6. Sektion: Log

| Filter | Visar |
|--------|-------|
| All | Alla loggrader |
| Errors | Endast ERROR |
| Warnings | WARN + ERROR |
| Info | INFO + ovan |

**Funktioner:**
- Sök i loggen (Ctrl+F)
- Kopiera markerade rader
- Auto-scroll (toggle)
- Exportera hela loggen

---

## 7. Actionbar

| Knapp | Funktion | Syns när |
|-------|----------|----------|
| 🔄 Retry | Starta om agenten | Failed / Completed |
| ⏹ Stop | Stoppa agenten | Running |
| 📋 Copy Output | Kopiera output till clipboard | Completed |
| 💾 Export | Exportera agentdata | Completed / Failed |
| ✕ Close | Stäng detaljvyn | Alltid |

---

## 8. Realtidsuppdatering (running agent)

När agenten körs:
- Duration uppdateras live (tickar varje sekund)
- Token counter ökar
- Output streamas in (append, inte replace)
- Logg uppdateras kontinuerligt
- Progress bar fylls baserat på förväntad tokens/tid

---

**Status:** Phase 0 — Design
