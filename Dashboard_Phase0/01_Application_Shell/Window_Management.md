# Window Management

**StydeForge Dashboard — Mission Control**
**Phase 0 Design Document**

---

## 1. Fönsterbeteende

```
┌─────────────────────────────────────────────────────────────┐
│ StydeForge — Mission Control                          ─ □ ✕│
├─────────────────────────────────────────────────────────────┤
│ [▶ Start] [⏸ Pausa] [⏹ Stopp] [⚙ Inställningar]           │
├──────────────────┬──────────────────────┬───────────────────┤
│                  │                      │                   │
│   AGENT PANEL    │   BENCHMARK PANEL    │   CHATT PANEL     │
│   (30%)          │   (35%)              │   (35%)           │
│                  │                      │                   │
│                  │                      │                   │
│                  │                      │                   │
│                  │                      │                   │
├──────────────────┴──────────────────────┴───────────────────┤
│ Status: ● Forge Running | 3 agents active | 12.4K tokens  │
└─────────────────────────────────────────────────────────────┘
```

---

## 2. Specifikationer

| Egenskap | Värde | Notering |
|----------|-------|----------|
| Startstorlek | 1400×900 px | Rymmer alla 3 paneler bekvämt |
| Minsta storlek | 900×600 px | Paneler kollapsar till tabbar under 1100px |
| Maximerad | Fullskärm med marginaler | Optimerad för 1080p och 1440p |
| Position | Kommer ihåg senaste position | Sparas i config |
| Alltid överst | Toggle (⚙ inställningar) | För "mission control"-känsla |
| Titelrad | Anpassad mörk titelrad | Inte Windows default |
| Runda hörn | 8px border-radius | Modern känsla |

---

## 3. Panel-layout

### 3.1 Standardlayout (≥1400px bredd)

```
┌──────────┬───────────────┬───────────────┐
│ AGENTS   │ BENCHMARKS    │ CHATT         │
│          │               │               │
│ 30%      │ 35%           │ 35%           │
│          │               │               │
│ scroll   │ scroll        │ scroll        │
└──────────┴───────────────┴───────────────┘
```

### 3.2 Kompakt layout (900-1399px)

```
┌─────────────────┬──────────────────────┐
│ AGENTS (40%)    │ CHATT (60%)          │
│                 │                      │
│ BENCHMARKS (dold│                      │
│  — tabb högst   │                      │
│  upp)           │                      │
└─────────────────┴──────────────────────┘
```

### 3.3 Minimal layout (<900px)

```
┌────────────────────────────────────────┐
│ [Agenter] [Benchmarks] [Chatt]  ← tabs│
├────────────────────────────────────────┤
│                                        │
│          Aktiv panel                   │
│                                        │
└────────────────────────────────────────┘
```

---

## 4. Dragbara paneler

Alla panelgränser är **dragbara** (resize handles):
- Muspekaren ändras till ↔ vid panelgräns
- Minsta panelbredd: 200px
- Layout sparas i localStorage och återställs vid nästa start
- Dubbelklick på panelgräns → återställ till default-proportion

---

## 5. Tangentbordsgenvägar

| Genväg | Funktion |
|--------|----------|
| `Ctrl+1` | Fokusera Agent-panelen |
| `Ctrl+2` | Fokusera Benchmark-panelen |
| `Ctrl+3` | Fokusera Chatt-panelen |
| `Ctrl+Shift+S` | Starta Forge |
| `Ctrl+Shift+P` | Pausa Forge |
| `Ctrl+Shift+X` | Stoppa Forge |
| `Ctrl+,` | Öppna inställningar |
| `Ctrl+K` | Fokusera chatt-input |
| `Escape` | Blur/a från input-fält |

---

## 6. Multi-monitor

| Scenario | Beteende |
|----------|----------|
| Primär skärm (1080p) | Standardlayout, maximerad |
| Sekundär skärm (1440p 4K) | Flytta fönster = behåll layout, skala upp |
| 3+ skärmar | Kom ihåg position per skärm-konfiguration |

---

## 7. Fönsterstatus

Fönsterstatus sparas i config:
```json
{
  "window": {
    "x": 100,
    "y": 50,
    "width": 1400,
    "height": 900,
    "maximized": false,
    "always_on_top": false,
    "panels": {
      "agents_width": 0.30,
      "benchmarks_width": 0.35
    }
  }
}
```

---

**Status:** Phase 0 — Design
