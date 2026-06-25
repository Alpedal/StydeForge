# Layout Design

**StydeForge Dashboard — Mission Control**
**Phase 0 Design Document**

---

## 1. Huvudlayout

```
┌──────────────────────────────────────────────────────────────────┐
│ StydeForge — Mission Control                              ─ □ ✕ │
├──────────────────────────────────────────────────────────────────┤
│ [▶ Start] [⏸ Pausa] [⏹ Stopp]  │ ⚙ │ ● Forge Running — 3 agents │
├──────────────┬───────────────────────┬───────────────────────────┤
│              │                       │                           │
│   AGENTS     │    BENCHMARKS         │    CHATT                  │
│              │                       │                           │
│ ┌──────────┐ │  ┌─────────────────┐  │ ┌───────────────────────┐ │
│ │Agent 1   │ │  │  Tokens/s ████  │  │ │ User: optimera min    │ │
│ │● running │ │  │  Latency  ██    │  │ │ config                │ │
│ │deepseek  │ │  │  Cost     █     │  │ │                       │ │
│ └──────────┘ │  └─────────────────┘  │ │ Agent: Jag läser din   │ │
│ ┌──────────┐ │                       │ │ config.yaml...         │ │
│ │Agent 2   │ │  ┌─────────────────┐  │ │                       │ │
│ │✓ klar    │ │  │  Eval Scores    │  │ │ ───────────────────── │ │
│ │87/100    │ │  │  ████████░░ 78% │  │ │                       │ │
│ └──────────┘ │  └─────────────────┘  │ │ [Skriv meddelande...] │ │
│ ┌──────────┐ │                       │ └───────────────────────┘ │
│ │Agent 3   │ │  ┌─────────────────┐  │                           │
│ │✗ felat  │ │  │  Modell-jmf     │  │                           │
│ └──────────┘ │  └─────────────────┘  │                           │
│              │                       │                           │
├──────────────┴───────────────────────┴───────────────────────────┤
│ Status: ● Running | 3/3 agents | 12,432 tokens | $0.037 cost     │
└──────────────────────────────────────────────────────────────────┘
```

---

## 2. Grid-system

CSS Grid-baserad layout:

```css
.dashboard {
  display: grid;
  grid-template-columns: 30% 35% 35%;
  grid-template-rows: auto 1fr auto;
  grid-template-areas:
    "titlebar titlebar titlebar"
    "agents   benches  chat"
    "status   status   status";
  height: 100vh;
}
```

| Grid-area | Innehåll | Höjd |
|-----------|----------|------|
| `titlebar` | Anpassad titelrad + kontrollknappar + status | 48px |
| `agents` | Agentpanel — scrollbar lista | flex 1 |
| `benches` | Benchmarkpanel — grafer | flex 1 |
| `chat` | Chattpanel — meddelanden + input | flex 1 |
| `status` | Statusfält — tokens, kostnad, agenter | 32px |

---

## 3. Titelrad

```
┌──────────────────────────────────────────────────────────────────┐
│ [S] StydeForge — Mission Control                          ─ □ ✕ │
├──────────────────────────────────────────────────────────────────┤
│ [▶ Start] [⏸ Pausa] [⏹ Stopp] │ ⚙ │ ● Forge Running — 3 active │
└──────────────────────────────────────────────────────────────────┘
```

| Element | Position | Beskrivning |
|---------|----------|-------------|
| Logotyp | Vänster, 24×24px | "S" i en hexagon |
| Titel | Vänster, efter logotyp | "StydeForge — Mission Control" |
| Kontrollknappar | Vänster, efter titel | Start/Pausa/Stopp med ikoner |
| Inställningar | Höger, före fönsterknappar | ⚙ kugghjulsikon |
| Statusindikator | Mellan kontroller och ⚙ | Grön/Gul/Röd prick + text |
| Fönsterknappar | Längst till höger | Minimera/Maximera/Stäng |

---

## 4. Panel-design

### 4.1 Panel-header

Varje panel har en header:

```
┌────────────────────────┐
│ 📡 AGENTS    [📌] [✕] │  ← header: ikon + titel + pin/close
├────────────────────────┤
│                        │
│   Innehåll             │  ← scrollbart innehåll
│                        │
└────────────────────────┘
```

### 4.2 Panel-egenskaper

| Egenskap | Beskrivning |
|----------|-------------|
| Header-höjd | 36px |
| Bakgrund | `#1a1a2e` (mörkblå) |
| Kantlinje | 1px `#2a2a4a` mellan paneler |
| Scrollbar | Anpassad tunn (6px), mörk |
| Min-bredd | 200px |
| Resize handle | 4px bred, ändrar cursor till `col-resize` |

### 4.3 Panel-tabbar (kompakt läge)

När fönstret är <1100px brett:

```
┌──────────────────────┐
│ [Agenter][Bench][Chat]│  ← tabbar
├──────────────────────┤
│                      │
│  Aktiv panel         │
│                      │
└──────────────────────┘
```

---

## 5. Statusfält

```
┌──────────────────────────────────────────────────────────────────┐
│ ● Running | 3 agents | 12,432 tokens | $0.037 | ⚡ 45 t/s | 23°C│
└──────────────────────────────────────────────────────────────────┘
```

| Fält | Format | Uppdatering |
|------|--------|-------------|
| Status | ●/○ ikon + text | Vid tillståndsbyte |
| Agenter | "X agents" | Var 2s |
| Tokens | "12.4K tokens" | Var 10s |
| Kostnad | "$0.037" | Var 10s |
| Hastighet | "⚡ 45 t/s" | Var 10s |
| CPU-temp | "23°C" | Var 30s |

---

## 6. Responsiv design

| Brytpunkt | Layout |
|-----------|--------|
| ≥1400px | Tre paneler sida vid sida (30/35/35) |
| 1100-1399px | Två paneler (agents + chatt), benchmarks som tab |
| 900-1099px | Två paneler (40/60) |
| <900px | En panel i taget med tabbar |

---

## 7. Tomma tillstånd

| Tillstånd | Visning |
|-----------|---------|
| Inga agenter | "No agents active. Start Forge or spawn manually." + [Start Forge]-knapp |
| Inga benchmarks | "No benchmark data yet. Run agents to collect metrics." |
| Tom chatt | "StydeForge Chat — ask me anything. I can read/write files, run commands, and use skills." |
| Forge ej startad | Alla paneler visar tomma tillstånd, statusfält: "● Stopped" |

---

**Status:** Phase 0 — Design
