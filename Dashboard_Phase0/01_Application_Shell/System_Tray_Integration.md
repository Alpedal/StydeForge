# System Tray Integration

**StydeForge Dashboard — Mission Control**
**Phase 0 Design Document**

---

## 1. Översikt

Dashboarden minimeras till system tray snarare än att stängas helt — så att Forge kan fortsätta köras i bakgrunden.

```
Windows System Tray (aktivitetsfältet, höger sida)
┌──────────────────────────────────────────┐
│  ▲  🌐  🔊  [S]  15:34              │
│              ↑                          │
│         StydeForge-ikon                 │
└──────────────────────────────────────────┘

Högerklick på ikonen:
┌────────────────────┐
│ Öppna Dashboard    │
│ ─────────────      │
│ Status: ● Running  │
│ Agenter: 3 aktiva  │
│ Tokens: 12.4K      │
│ ─────────────      │
│ ▶ Starta Forge     │
│ ⏸ Pausa Forge     │
│ ⏹ Stoppa Forge    │
│ ─────────────      │
│ ⚙ Inställningar   │
│ ─────────────      │
│ Avsluta            │
└────────────────────┘
```

---

## 2. Tray-ikon

| Egenskap | Beskrivning |
|----------|-------------|
| Ikon | Stiliserad "S" (StydeForge-logotyp) — 16×16 och 32×32 px |
| Färg (aktiv) | Grön — Forge körs |
| Färg (pausad) | Gul — Forge pausad |
| Färg (inaktiv) | Grå — Forge stoppad |
| Färg (fel) | Röd — Forge kraschade eller fel |

---

## 3. Minimera-beteende

| Åtgärd | Resultat |
|--------|----------|
| Klicka ✕ (stäng-knappen) | Minimera till tray (om Forge kör) |
| Klicka ✕ (Forge ej aktiv) | Fråga: "Vill du stänga eller minimera?" |
| Dubbelklicka tray-ikon | Öppna/återställ Dashboard-fönstret |
| Högerklicka → Öppna Dashboard | Återställ fönster, fokusera |
| Windows+D (visa skrivbord) | Dashboard minimeras normalt |
| Alt+Tab | Dashboard syns i Alt+Tab-listan |

---

## 4. Notiser

Dashboarden skickar Windows-notiser vid viktiga händelser:

| Händelse | Notis |
|----------|-------|
| Agent klar (score ≥80) | "✅ Agent 'code-reviewer v3' klar! Score: 87/100" |
| Agent klar (score <80) | "⚠ Agent 'sql-helper' fick 62/100 — under quality gate" |
| Forge kraschade | "🔴 StydeForge kraschade. Klicka för att se logg." |
| Hög resursanvändning | "⚠ CPU 92% i 30s — kan påverka prestanda" |
| Checkpoint skapad | "💾 Checkpoint sparad: 2026-06-25 15:42" |
| Ny version tillgänglig | "🔄 StydeForge v1.1 finns! Klicka för att uppdatera." |

---

## 5. Notis-interaktion

| Klick på notis | Resultat |
|----------------|----------|
| Agent-notis | Öppna Dashboard → fokusera agentens detaljvy |
| Krasch-notis | Öppna Dashboard → visa fellogg |
| Uppdaterings-notis | Starta uppdateringsprocessen |

---

## 6. Konfiguration

```json
{
  "tray": {
    "minimize_to_tray": true,
    "show_notifications": true,
    "notification_events": [
      "agent_completed",
      "agent_failed",
      "forge_crashed",
      "update_available"
    ],
    "start_minimized": false
  }
}
```

| Inställning | Default | Beskrivning |
|-------------|---------|-------------|
| `minimize_to_tray` | true | Stäng = minimera till tray |
| `show_notifications` | true | Visa Windows-notiser |
| `notification_events` | ["agent_completed", "agent_failed", "forge_crashed", "update_available"] | Vilka händelser som triggar notis |
| `start_minimized` | false | Starta Dashboard minimerad vid Windows-start |

---

## 7. Edge Cases

| Scenario | Beteende |
|----------|----------|
| Dashboard startas medan Forge redan körs (från tidigare session) | Detektera befintlig process, koppla till den, fråga inte |
| Windows Explorer kraschar/startas om | Tray-ikonen återskapas automatiskt |
| Användare "stänger" via tray medan Forge kör | Fråga: "Forge körs. Vill du: [Stoppa Forge + Avsluta] [Minimera till tray] [Avbryt]" |
| Tray-ikon syns inte (Windows döljer) | Använd systemets "visa alla ikoner"-funktion; Dashboard visar instruktion vid första körning |

---

**Status:** Phase 0 — Design
