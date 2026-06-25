# Agent Tracking

**StydeForge Dashboard — Mission Control**
**Phase 0 Design Document**

---

## 1. Översikt

Agent-panelen visar alla aktiva, slutförda, och felade agenter i realtid. Data hämtas från Hermes CLI (`hermes process list --json`) och pollas var 2:a sekund.

---

## 2. Agentyer

| Typ | Källa | Beskrivning |
|-----|-------|-------------|
| **Forge Agent** | `hermes process list` | Agenter spawnade av Forge-loopen |
| **Cron Job Agent** | `hermes cronjob list` | Schemalagda jobb som körs |
| **Manual Agent** | Spawnad via Dashboardens "New Agent"-knapp | Manuellt startad agent |
| **Chat Agent** | Chat-panelen | Agenten som körs i chatten (visas om den spawnar sub-agenter) |

---

## 3. Agentlista — visuell design

```
┌──────────────────────────────────────────┐
│ 📡 AGENTS                        [3] [↻]│
├──────────────────────────────────────────┤
│                                          │
│ ● code-reviewer-v3              87/100  │
│   deepseek-v4-flash · 2m 34s            │
│   4.2K tokens · $0.012 · ⚡ 45 t/s       │
│   ████████████████████░░ 87%            │
│                                          │
│ ● test-generator-v2             64/100  │
│   deepseek-v4-flash · 1m 12s            │
│   2.1K tokens · $0.006 · ⚡ 38 t/s       │
│   ████████████░░░░░░░░ 64%              │
│                                          │
│ ✓ doc-writer-v1                  92/100 │
│   deepseek-v4-pro · 3m 45s              │
│   6.8K tokens · $0.019                   │
│   ✅ Completed                           │
│                                          │
│ ✗ sql-helper-v2                   FAIL  │
│   deepseek-v4-flash · 0m 23s            │
│   Error: API timeout                     │
│   🔄 Retry                              │
│                                          │
└──────────────────────────────────────────┘
```

---

## 4. Agent-card — fält

| Fält | Källa | Format |
|------|-------|--------|
| **Status** | Hermes process status | ● running / ✓ done / ✗ failed / ⏸ paused |
| **Namn** | Agentens blueprint-namn | `code-reviewer-v3` |
| **Score** | Eval-resultat | `87/100` (visas bara när klar) |
| **Modell** | Vilken modell agenten använder | `deepseek-v4-flash` |
| **Tid** | Hur länge den kört | `2m 34s` (running) / `3m 45s` (klar) |
| **Tokens** | Tokens använda | `4.2K` |
| **Kostnad** | Uppskattad kostnad | `$0.012` |
| **Hastighet** | Tokens per sekund | `⚡ 45 t/s` (endast running) |
| **Förloppsbar** | % av förväntad tid/tokens | ████████░░ 87% |
| **Fel** | Felmeddelande (om failed) | `API timeout` |

---

## 5. Live-uppdatering

```
┌─────────────────────────────────────────┐
│           Poll Loop (var 2s)            │
│                                         │
│  hermes process list --json             │
│         │                               │
│         ▼                               │
│  ┌──────────────────┐                   │
│  │ Jämför med cache │                   │
│  └────┬─────────┬───┘                   │
│       │         │                       │
│   Ny agent   Statusändring              │
│       │         │                       │
│       ▼         ▼                       │
│  ┌────────┐ ┌──────────┐               │
│  │Lägg till│ │Uppdatera │               │
│  │i lista │ │befintlig │               │
│  │(slide) │ │(morph)   │               │
│  └────────┘ └──────────┘               │
└─────────────────────────────────────────┘
```

**Optimeringar:**
- Diffa JSON — skicka bara ändringar till UI
- Bara rendera om synliga agenter (virtual scroll för 50+ agenter)
- Status-prick animeras endast för running agents

---

## 6. Filtrering & Sortering

```
┌──────────────────────────────────────────┐
│ 📡 AGENTS          [Alla ▼] [Senaste ▼] │
├──────────────────────────────────────────┤
```

| Filter | Alternativ |
|--------|------------|
| Status | Alla, Running, Completed, Failed, Paused |
| Modell | Alla, deepseek-v4-flash, deepseek-v4-pro, ... |
| Typ | Alla, Forge, Cron, Manual |

| Sortering | Beskrivning |
|-----------|-------------|
| Senaste | Senast uppdaterad först (default) |
| Score | Högsta score först |
| Tid | Längst körtid först |
| Kostnad | Högst kostnad först |

---

## 7. Interaktioner

| Interaktion | Resultat |
|-------------|----------|
| **Klicka på agent** | Öppna Agent Detail View (högerpanel eller modal) |
| **Dubbelklicka** | Fokusera agentens output/logg |
| **Högerklicka** | Kontextmeny: Visa detaljer, Stoppa, Starta om, Exportera |
| **✗ på failed agent** | Dismiss (dölj från listan, spara i historik) |
| **🔄 på failed agent** | Retry — starta om agenten med samma parametrar |

---

## 8. Tomma tillstånd

| Scenario | Visning |
|----------|---------|
| Forge ej startad | "No agents active. [Start Forge] to begin spawning." |
| Forge startad men inga agenter än | "Forge is running. Waiting for first agent spawn..." + spinner |
| Alla agenter klara | "All agents completed. 3/3 passed quality gate (≥80)." |
| Filter ger 0 resultat | "No agents match filter 'Failed'. [Rensa filter]" |

---

## 9. Agent-historik

Slutförda agenter sparas i lokal databas (IndexedDB):
- Visa senaste 100 agenter
- Sök bland historiska agenter
- Exportera agentdata som JSON/CSV

---

**Status:** Phase 0 — Design
