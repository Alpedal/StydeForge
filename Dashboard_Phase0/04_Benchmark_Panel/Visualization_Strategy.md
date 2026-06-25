# Visualization Strategy

**StydeForge Dashboard — Mission Control**
**Phase 0 Design Document**

---

## 1. Översikt

Alla grafer och visualiseringar i Dashboarden använder **Chart.js** — ett lättviktigt (<60KB gzippat) canvas-bibliotek med mörkt tema-stöd.

---

## 2. Chart.js — val och motivering

| Faktor | Chart.js | Alternativ (D3.js) | Alternativ (ECharts) |
|--------|----------|---------------------|----------------------|
| Storlek | ~58KB gzip | ~80KB+ (modulärt) | ~300KB+ |
| Komplexitet | Låg API | Hög — bygger allt från grunden | Medium |
| Prestanda | Bra (Canvas) | Utmärkt (SVG/Canvas) | Bra |
| Mörkt tema | Enkelt (plugin) | Manuellt | Inbyggt |
| Animation | Inbyggd | Manuell | Inbyggd |
| Val | ✅ | Endast vid behov av custom visualisering | För tungt |

---

## 3. Chart-konfiguration (global)

```javascript
Chart.defaults.color = '#8892b0';           // Textfärg
Chart.defaults.borderColor = '#2a2a4a';     // Gridlines
Chart.defaults.backgroundColor = '#1a1a2e'; // Chart-bakgrund
Chart.defaults.font.family = "'JetBrains Mono', monospace";
Chart.defaults.font.size = 11;
Chart.defaults.plugins.tooltip.backgroundColor = '#16213e';
Chart.defaults.plugins.tooltip.borderColor = '#2a2a4a';
```

---

## 4. Chart-typer

### 4.1 Line Chart — Tokens per Second

```javascript
{
  type: 'line',
  data: {
    datasets: [
      { label: 'deepseek-v4-flash', borderColor: '#6366f1', tension: 0.3 },
      { label: 'deepseek-v4-pro', borderColor: '#10b981', tension: 0.3 },
    ]
  },
  options: {
    scales: {
      x: { type: 'time', time: { unit: 'minute' } },
      y: { title: { text: 'Tokens/s' }, beginAtZero: true }
    },
    plugins: {
      annotation: { /* markörer för agent-spawn */ }
    }
  }
}
```

### 4.2 Stacked Area — Cost Over Time

```javascript
{
  type: 'line',
  data: {
    datasets: [
      { label: 'deepseek-v4-flash', fill: true, stacked: true },
      { label: 'deepseek-v4-pro', fill: true, stacked: true },
    ]
  },
  options: {
    scales: {
      y: { stacked: true, title: { text: 'Cost (USD)' } }
    }
  }
}
```

### 4.3 Bar Chart — Agent Throughput

```javascript
{
  type: 'bar',
  data: {
    datasets: [
      { label: 'Score ≥80', backgroundColor: '#10b981' },
      { label: 'Score 60-79', backgroundColor: '#f59e0b' },
      { label: 'Score <60', backgroundColor: '#ef4444' },
    ]
  },
  options: {
    scales: {
      x: { stacked: true },
      y: { stacked: true, title: { text: 'Agents' } }
    }
  }
}
```

### 4.4 Scatter Chart — Score Distribution

Varje punkt = en agent. X-axel = tid, Y-axel = score. Färg = modell.

### 4.5 Gauge — Live Tokens/s

En cirkulär gauge som visar nuvarande tokens/s mot genomsnittet.

### 4.6 Sparkline — Miniatyrgrafer

I agent-cards och tabell-celler: små 1D-grafer som visar trend.

---

## 5. Färgpalett (datasets)

| Index | Färg | Användning |
|-------|------|------------|
| 0 | `#6366f1` | deepseek-v4-flash (primary) |
| 1 | `#10b981` | deepseek-v4-pro (success) |
| 2 | `#3b82f6` | gpt-4o (info) |
| 3 | `#f59e0b` | claude (warning) |
| 4 | `#ef4444` | errors/failures |
| 5 | `#8b5cf6` | custom provider 1 |
| 6 | `#06b6d4` | custom provider 2 |

---

## 6. Interaktion

| Interaktion | Beteende |
|-------------|----------|
| Hover | Tooltip med exakta värden |
| Klicka på legend | Toggle dataset synlighet |
| Dubbelklicka | Zooma in på det området |
| Scroll | Zooma in/ut på tidsaxel |
| Drag | Panna i tidsaxeln |
| Högerklicka | "Reset zoom" |

---

## 7. Annoteringar

Viktiga händelser markeras på graferna:

| Händelse | Markör |
|----------|--------|
| Agent spawn | Liten vertikal linje + agent-namn |
| Agent klar (score ≥80) | Grön prick + score |
| Agent felad | Röd prick + feltyp |
| Checkpoint | Blå romb + "💾" |
| Model switch | Vertikal linje + modellnamn |

---

## 8. Prestanda

| Optimering | Beskrivning |
|------------|-------------|
| Datapunktsbegränsning | Max 500 punkter per dataset (decimering) |
| Canvas, inte SVG | Canvas är snabbare för många datapunkter |
| RequestAnimationFrame | Animerar endast synliga grafer |
| Lata grafer | Rendera bara när panelen är synlig |
| Datacache | Cachad aggregerad data — rita om utan API-anrop |

---

## 9. Export

| Format | Användning |
|--------|------------|
| PNG | Screenshot av graf |
| CSV | Rådata export |
| JSON | Full data export |

---

**Status:** Phase 0 — Design
