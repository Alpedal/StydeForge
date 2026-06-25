# Process Control

**StydeForge Dashboard — Mission Control**
**Phase 0 Design Document**

---

## 1. Översikt

Dashboarden hanterar Hermes som en child-process via Tauris Rust-backend:

```
┌─────────────────────────────────┐
│   Dashboard (Tauri Rust Shell)  │
│                                 │
│   spawn("hermes", ["forge",     │
│          "start"])              │
│         │                       │
│         ▼                       │
│   ┌─────────────────────┐       │
│   │ Child Process       │       │
│   │ hermes forge start  │       │
│   │                     │       │
│   │ stdout/stderr pipes │       │
│   └─────────┬───────────┘       │
│             │                   │
│   ┌─────────┴───────────┐       │
│   │ Process Manager     │       │
│   │ • Monitor stdout    │       │
│   │ • Track PID         │       │
│   │ • Handle crashes    │       │
│   │ • Signal (SIGTERM)  │       │
│   └─────────────────────┘       │
└─────────────────────────────────┘
```

---

## 2. Process-spawning

### 2.1 Hermes Forge Loop

```rust
// Pseudokod — Tauri Rust-backend
let hermes_process = Command::new("hermes")
    .args(["forge", "start"])
    .env("HERMES_PROFILE", "default")
    .stdout(Stdio::piped())
    .stderr(Stdio::piped())
    .spawn()?;

// Spara PID för senare kontroll
state.hermes_pid = hermes_process.id();
```

### 2.2 Ad-hoc Hermes-kommandon

Dashboarden kör även kortlivade Hermes-kommandon för datahämtning:

```rust
// Polla agentstatus — körs och returnerar direkt
let output = Command::new("hermes")
    .args(["process", "list", "--json"])
    .output()?;

let agents: Vec<Agent> = serde_json::from_str(&output.stdout)?;
```

---

## 3. Processövervakning

Dashboarden övervakar Hermes-processen kontinuerligt:

| Händelse | Detektering | Åtgärd |
|----------|-------------|--------|
| Process kraschar | `WaitForSingleObject` på process handle | Visa felmeddelande, erbjud omstart |
| Process hänger sig | Inget stdout på 60s | Varning i UI, erbjud force kill |
| Hög CPU | >80% i 30s | Varning: "Hermes använder mycket CPU" |
| Hög minnesanvändning | >4GB | Varning: "Hermes använder mycket minne" |

---

## 4. Kommunikation med Hermes

### 4.1 stdout/stderr — Realtidslogg

Hermes stdout och stderr pipes läses kontinuerligt:
- Varje rad parsas (JSON-lines format från Hermes loggning)
- Relevant data skickas till UI:t (statusuppdateringar, fel)
- Råa loggar sparas till disk för felsökning

### 4.2 Signalhantering

| Signal | Användning |
|--------|------------|
| SIGTERM | "Stoppa graceful" — Hermes avslutar aktiv iteration |
| SIGINT | "Pausa" — Hermes pausar efter nuvarande steg |
| SIGKILL | Force kill (sista utväg, efter timeout) |

---

## 5. Cron-jobb-hantering

Dashboarden kan styra Hermes cron-jobb:

| Åtgärd | Hermes-kommando |
|--------|-----------------|
| Lista alla jobb | `hermes cronjob list --json` |
| Starta ett jobb | `hermes cronjob resume <job_id>` |
| Pausa ett jobb | `hermes cronjob pause <job_id>` |
| Köra ett jobb manuellt | `hermes cronjob run <job_id>` |
| Ta bort ett jobb | `hermes cronjob remove <job_id>` |

---

## 6. Error Recovery

```
Process krasch detekterad
        │
        ▼
┌─────────────────────┐
│ 1. Logga kraschen   │
│    (spara sista     │
│     100 raderna av  │
│     stderr)         │
└────────┬────────────┘
         │
         ▼
┌─────────────────────┐
│ 2. Visa dialog      │
│ "Hermes kraschade.  │
│  Vill du:           │
│  [Starta om]        │
│  [Visa logg]        │
│  [Stäng Dashboard]" │
└────────┬────────────┘
         │
         ▼
┌─────────────────────┐
│ 3. Om omstart:      │
│    • Ladda senaste  │
│      checkpoint     │
│    • Återställ      │
│      agenter        │
│    • Starta om      │
│      Forge-loopen   │
└─────────────────────┘
```

---

## 7. Process-säkerhet

| Regel | Orsak |
|-------|-------|
| Endast en Forge-process åt gången | Undvik race conditions |
| Dashboard äger processen | Om Dashboard dör → Hermes dör (inga zombies) |
| Timeout på 30s vid shutdown | Förhindra att shutdown hänger sig |
| Logga alla process-händelser | Full spårbarhet för felsökning |

---

**Status:** Phase 0 — Design
