# Lifecycle Management

**StydeForge Dashboard — Mission Control**
**Phase 0 Design Document**

---

## 1. Översikt

Dashboarden är master-controllern för hela StydeForge-systemet. Tre tillstånd:

```
         ┌──────────────┐
    ┌───→│  STOPPED     │←───┐
    │    └──────┬───────┘    │
    │           │ [Start]    │ [Stopp]
    │           ▼            │
    │    ┌──────────────┐    │
    │    │  RUNNING     │────┘
    │    └──────┬───────┘
    │           │ [Pausa]
    │           ▼
    │    ┌──────────────┐
    └────│  PAUSED      │
         └──────────────┘
              │ [Start] = Återuppta
```

---

## 2. Tillstånd: STOPPED

**Indikator i UI:** Röd status-prick + "StydeForge: Inaktiv"

| Egenskap | Värde |
|----------|-------|
| Hermes-processer | Inga Forge-processer körs |
| Cron-jobb | Pausade |
| Agent-panel | Visar "Inga agenter aktiva" |
| Systemresurser | Dashboard använder minimal CPU/RAM |
| Vid exit | Fråga om Forge ska startas |

---

## 3. Tillstånd: RUNNING

**Indikator i UI:** Grön status-prick + "StydeForge: Kör — 3 agenter aktiva"

**Vad som startas:**

```
Dashboard [▶ Start]
     │
     ├─→ 1. Validera config
     │      └─ Kolla att alla providers har API-nycklar
     │
     ├─→ 2. Starta Hermes Forge-loop
     │      └─ Spawna som child-process: hermes forge start
     │
     ├─→ 3. Starta cron-jobb
     │      └─ Om auto-start av specifika jobb är konfigurerat
     │
     └─→ 4. Börja polla agentstatus
            └─ Var 2:a sekund: hermes process list
```

**Child-process management:**
- Hermes körs som en child-process under Dashboarden
- Dashboarden äger processens livscykel
- Om Dashboarden kraschar → Hermes stängs (graceful)
- Om Hermes kraschar → Dashboarden visar fel och erbjuder omstart

---

## 4. Tillstånd: PAUSED

**Indikator i UI:** Gul status-prick + "StydeForge: Pausad"

**Vad som pausas:**

| Komponent | Åtgärd |
|-----------|--------|
| Agent-spawning | Inga nya agenter startas |
| Aktiva agenter | Får slutföra nuvarande uppgift |
| Cron-jobb | Temporärt inaktiva (sätts i paus-läge) |
| Eval-pipeline | Pausas efter nuvarande eval |
| Dashboard UI | Fortsätter uppdatera, men visar paus-status |

**Återuppta:** Klicka [▶ Start] → allt återupptas där det var.

---

## 5. Stopp-sekvens (graceful shutdown)

```
Dashboard [⏹ Stopp]
     │
     ├─→ 1. Visa bekräftelsedialog
     │      "Stoppa StydeForge? 3 agenter körs just nu."
     │      [Stoppa ändå] [Vänta tills klart] [Avbryt]
     │
     ├─→ 2. Signalera stopp till Forge-loopen
     │      └─ Låt nuvarande agenter avsluta (timeout: 30s)
     │
     ├─→ 3. Spara checkpoints
     │      └─ Alla pågående agenter sparar sitt tillstånd
     │
     ├─→ 4. Stäng cron-jobb
     │      └─ Sätt status=paused på alla aktiva cron-jobb
     │
     ├─→ 5. Döda Hermes-processen
     │      └─ SIGTERM → vänta 5s → SIGKILL
     │
     └─→ 6. Uppdatera UI
            └─ Röd prick, "StydeForge: Inaktiv"
```

---

## 6. Edge Cases

| Scenario | Beteende |
|----------|----------|
| Användare stänger fönstret (✕) | Om Forge körs: minimera till tray (default) eller fråga |
| Användare stänger via tray | Stäng helt (om Forge ej kör) eller fråga |
| Windows stängs av / reboot | Dashboarden fångar WM_QUERYENDSESSION → graceful shutdown |
| Strömavbrott / force kill | Nästa start: "Forge stängdes oväntat. Återställ?" → kör recovery |
| Dubbelstart av Dashboard | Andra instansen detekterar första → fokusera befintligt fönster → avsluta |

---

## 7. Auto-start

Valfritt (⚙ inställningar):
- Starta Dashboard med Windows
- Auto-starta Forge när Dashboard öppnas
- Starta minimerad till system tray

---

**Status:** Phase 0 — Design
