# Spawn New Agent

**StydeForge Dashboard — Mission Control**
**Phase 0 Design Document**

---

## 1. Översikt

Dashboarden låter användaren manuellt spawna en ny agent — utan att gå via Forge-loopen. Detta är för ad-hoc uppgifter: "kör code review på den här filen", "generera tester för den här modulen".

---

## 2. Spawn-flöde

```
Klicka [+ New Agent] i agent-panelen
        │
        ▼
┌─────────────────────────────────┐
│  SPAWN NEW AGENT                │
│                                 │
│  ┌─ Blueprint ────────────────┐ │
│  │ [Välj blueprint ▼]         │ │
│  │  • code-review             │ │
│  │  • test-generator          │ │
│  │  • doc-writer              │ │
│  │  • custom (skriv prompt)   │ │
│  └────────────────────────────┘ │
│                                 │
│  ┌─ Model ────────────────────┐ │
│  │ [deepseek-v4-flash ▼]      │ │
│  └────────────────────────────┘ │
│                                 │
│  ┌─ Skills ───────────────────┐ │
│  │ ☑ code-review              │ │
│  │ ☐ test-driven-development  │ │
│  │ ☑ systematic-debugging     │ │
│  │ [+ Add skill]              │ │
│  └────────────────────────────┘ │
│                                 │
│  ┌─ Prompt / Instructions ────┐ │
│  │ ┌─────────────────────────┐ │ │
│  │ │ Granska auth-service.ts │ │ │
│  │ │ för säkerhetshål       │ │ │
│  │ │                         │ │ │
│  │ └─────────────────────────┘ │ │
│  └────────────────────────────┘ │
│                                 │
│  ┌─ Options ──────────────────┐ │
│  │ ☐ Caveman Ultra mode      │ │
│  │ ☑ Save output to file     │ │
│  │ ☐ Evaluate after complete │ │
│  └────────────────────────────┘ │
│                                 │
│  [Cancel]              [▶ Spawn]│
└─────────────────────────────────┘
```

---

## 3. Formulärfält

| Fält | Typ | Default | Beskrivning |
|------|-----|---------|-------------|
| Blueprint | Dropdown | Senast använda | Välj från tillgängliga blueprints eller "custom" |
| Model | Dropdown | deepseek-v4-flash | Välj modell från aktiva providers |
| Skills | Multi-select | Tom | Välj skills att ladda |
| Prompt | Textarea | Tom | Instruktioner till agenten |
| Caveman Ultra | Checkbox | true | Aktivera Caveman Ultra (70% färre tokens) |
| Save output | Checkbox | true | Spara output till fil |
| Evaluate | Checkbox | true | Kör eval efter slutförande |

---

## 4. Blueprint-system

### 4.1 Inbyggda blueprints

| Blueprint | Beskrivning | Default skills |
|-----------|-------------|----------------|
| `code-review` | Granska kod | requesting-code-review, systematic-debugging |
| `test-generator` | Generera tester | test-driven-development |
| `doc-writer` | Skriv dokumentation | — |
| `refactor` | Refaktorisera kod | simplify-code |
| `debug` | Felsök ett problem | systematic-debugging |
| `custom` | Fritext-prompt | Manuellt val |

### 4.2 Custom blueprints

Användaren kan spara egna blueprints:
```json
{
  "name": "my-security-audit",
  "description": "Granska kod för OWASP Top 10 sårbarheter",
  "model": "deepseek-v4-pro",
  "skills": ["systematic-debugging"],
  "caveman_ultra": false
}
```

---

## 5. Spawn-mekanism

När användaren klickar [▶ Spawn]:

```
1. Bygg kommando:
   hermes delegate_task \
     --goal "<prompt>" \
     --model "<model>" \
     --skills "<skill1>,<skill2>" \
     --caveman-ultra

2. Kör som child-process

3. Agent dyker upp i Agent-panelen med status ● Running

4. Output streamas till Agent Detail View
```

---

## 6. Validering

| Regel | Felmeddelande |
|-------|---------------|
| Prompt får inte vara tom | "Please enter instructions for the agent" |
| Minst en skill måste väljas (ej custom) | "Select at least one skill" |
| Modell måste ha en aktiv provider | "Provider not connected. Check settings." |

---

## 7. Quick-Spawn (genväg)

Från chatten: skriv `/spawn code-review auth-service.ts`

Chat-agenten tolkar kommandot och spawnar agenten utan att öppna formuläret.

---

**Status:** Phase 0 — Design
