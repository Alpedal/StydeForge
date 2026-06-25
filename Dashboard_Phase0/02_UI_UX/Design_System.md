# Design System

**StydeForge Dashboard — Mission Control**
**Phase 0 Design Document**

---

## 1. Designfilosofi

**"Terminal chic"** — mörkt, kompakt, funktionellt. Inget onödigt.

Inspiration:
- VS Code (mörkt tema, panel-layout)
- Linear (ren typografi, subtila animationer)
- htop/btm (kompakt data-visning)
- Cyberpunk 2077 UI (neon-accenter, glödande element)

---

## 2. Färgpalett

### 2.1 Primär palett

| Namn | Hex | Användning |
|------|-----|------------|
| **Background** | `#0d0d1a` | App-bakgrund, huvudfärg |
| **Surface** | `#1a1a2e` | Panel-bakgrund, kort |
| **Surface 2** | `#16213e` | Hover, alternerande rader |
| **Border** | `#2a2a4a` | Panelgränser, input-borders |
| **Text Primary** | `#e0e0e0` | Huvudtext |
| **Text Secondary** | `#8892b0` | Sekundär text, labels |
| **Text Muted** | `#4a5568` | Placeholder, inaktiv text |

### 2.2 Accent-färger

| Namn | Hex | Användning |
|------|-----|------------|
| **Primary** | `#6366f1` | Knappar, länkar, aktiv tab |
| **Primary Hover** | `#818cf8` | Hover på primary-element |
| **Success** | `#10b981` | Klar-status, positiva värden |
| **Warning** | `#f59e0b` | Pausad, varningar |
| **Danger** | `#ef4444` | Fel, stoppad, kritiskt |
| **Info** | `#3b82f6` | Information, neutral status |

### 2.3 Status-prickar

| Status | Färg | Glöd |
|--------|------|------|
| Running | `#10b981` | `box-shadow: 0 0 8px #10b981` |
| Paused | `#f59e0b` | `box-shadow: 0 0 8px #f59e0b` |
| Stopped | `#4a5568` | Ingen glöd |
| Error | `#ef4444` | `box-shadow: 0 0 8px #ef4444` (pulserande) |

---

## 3. Typografi

### 3.1 Font-stack

```css
font-family: 'JetBrains Mono', 'Fira Code', 'Cascadia Code',
             'Consolas', 'Monaco', monospace;
```

| Användning | Font | Storlek | Vikt |
|------------|------|---------|------|
| Titelrad | JetBrains Mono | 14px | 600 |
| Panel-headers | JetBrains Mono | 12px | 600 (uppercase, letter-spacing 1px) |
| Brödtext | JetBrains Mono | 13px | 400 |
| Kod | JetBrains Mono | 12px | 400 |
| Små labels | JetBrains Mono | 11px | 400 |
| Statusfält | JetBrains Mono | 11px | 500 |

### 3.2 Textstilar

| Stil | CSS |
|------|-----|
| Rubrik | `font-weight: 600; color: #e0e0e0;` |
| Brödtext | `font-weight: 400; color: #c0c0d0; line-height: 1.6;` |
| Kod | `background: #1a1a2e; border-radius: 4px; padding: 2px 6px;` |
| Länk | `color: #6366f1; text-decoration: none; &:hover { text-decoration: underline; }` |

---

## 4. Komponentbibliotek (design tokens)

### 4.1 Knappar

```css
/* Primär knapp */
.btn-primary {
  background: #6366f1;
  color: #ffffff;
  border: none;
  border-radius: 6px;
  padding: 6px 16px;
  font-size: 12px;
  font-weight: 600;
  cursor: pointer;
  transition: background 0.15s;
}
.btn-primary:hover { background: #818cf8; }
.btn-primary:active { background: #4f46e5; }

/* Sekundär knapp */
.btn-secondary {
  background: transparent;
  color: #c0c0d0;
  border: 1px solid #2a2a4a;
}

/* Danger knapp */
.btn-danger {
  background: #ef4444;
  color: #ffffff;
}
```

### 4.2 Input-fält

```css
.input {
  background: #0d0d1a;
  border: 1px solid #2a2a4a;
  border-radius: 6px;
  color: #e0e0e0;
  padding: 8px 12px;
  font-family: 'JetBrains Mono', monospace;
  font-size: 13px;
}
.input:focus {
  border-color: #6366f1;
  outline: none;
  box-shadow: 0 0 0 2px rgba(99, 102, 241, 0.2);
}
```

### 4.3 Scrollbars

```css
::-webkit-scrollbar { width: 6px; }
::-webkit-scrollbar-track { background: #0d0d1a; }
::-webkit-scrollbar-thumb {
  background: #2a2a4a;
  border-radius: 3px;
}
::-webkit-scrollbar-thumb:hover { background: #3a3a5a; }
```

---

## 5. Animationer

| Element | Animation | Duration | Easing |
|---------|-----------|----------|--------|
| Panel resize | `width` transition | 150ms | ease-out |
| Panel tab byte | `opacity` fade | 200ms | ease-in-out |
| Knapp hover | `background` färg | 150ms | ease |
| Status-prick (running) | `box-shadow` puls | 2s | infinite ease-in-out |
| Ny agent i lista | Slide-in från höger | 300ms | ease-out |
| Notis popup | Slide-in från botten | 400ms | ease-out |

---

## 6. Ikoner

Använder ett minimalt, konsistent ikon-set (Lucide-icons stil):

| Ikon | Användning |
|------|------------|
| ▶ | Starta |
| ⏸ | Pausa |
| ⏹ | Stoppa |
| ⚙ | Inställningar |
| ● | Status (med färg) |
| 📡 | Agent-panel |
| 📊 | Benchmark-panel |
| 💬 | Chatt-panel |
| 🔄 | Uppdatera |
| 📌 | Pin panel |
| ✕ | Stäng panel / ta bort |
| ⚡ | Hastighet / tokens per sekund |
| 💾 | Checkpoint / spara |

---

## 7. Dark Mode Only

Dashboarden är **endast mörkt tema** — inget ljust läge. Det är ett designbeslut, inte en inställning:

| Orsak |
|-------|
| Terminal-estetik — hör hemma i mörkret |
| Minskar kodkomplexitet (inga tema-variabler att hantera) |
| Bättre för långa sessioner (mindre ögonbelastning) |
| Matchar StydeForge Forge-terminalens utseende |

---

**Status:** Phase 0 — Design
