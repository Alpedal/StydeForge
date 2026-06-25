# StydeForge Dashboard — Phase 0 Index

**Projekt:** StydeForge Mission Control
**Typ:** Desktop-applikation — agentstyrning, övervakning, chatt
**Version:** 1.0
**Status:** Phase 0 — In Progress 🚧

---

## Dokumentöversikt (33 dokument)

| # | Sektion | Dokument | Beskrivning |
|---|---------|----------|-------------|
| 00 | Overview | `DASHBOARD_INDEX.md` | Detta index |
| 00 | Overview | `Dashboard_Vision.md` | Vision, mål, framgångskriterier |
| 00 | Overview | `Application_Architecture.md` | Hög-nivå arkitektur, komponenter, dataflöde |
| 01 | Application Shell | `Window_Management.md` | Fönsterhantering, storlek, position, multi-monitor |
| 01 | Application Shell | `Lifecycle_Management.md` | Starta/pausa/stänga hela StydeForge-systemet |
| 01 | Application Shell | `Process_Control.md` | Spawna/döda Hermes-processer, cron-jobb |
| 01 | Application Shell | `System_Tray_Integration.md` | Minimera till tray, statusindikator, notiser |
| 02 | UI/UX | `Layout_Design.md` | Grid-system, 3 paneler, resizable, responsivt |
| 02 | UI/UX | `Design_System.md` | Mörkt tema, typografi, färgpalett, ikoner |
| 02 | UI/UX | `Component_Library.md` | Återanvändbara komponenter (knappar, paneler, tabbar) |
| 03 | Agent Monitor | `Agent_Tracking.md` | Lista aktiva/klara/felade agenter, liveuppdatering |
| 03 | Agent Monitor | `Agent_Detail_View.md` | Detaljvy: tokens, kostnad, logg, output, historik |
| 03 | Agent Monitor | `Spawn_New_Agent.md` | Starta en agent manuellt från dashboarden |
| 04 | Benchmark Panel | `Performance_Metrics.md` | Tokens/s, latency, kostnad — per agent och modell |
| 04 | Benchmark Panel | `Quality_Benchmarks.md` | Eval-resultat (HumanEval, MMLU, custom benchmarks) |
| 04 | Benchmark Panel | `Visualization_Strategy.md` | Grafer, tidsserier, jämförelser — Chart.js |
| 05 | Chat Interface | `Chat_Architecture.md` | Chattfönster, streaming, markdown-rendering |
| 05 | Chat Interface | `Chat_Agent_Tools.md` | Verktyg: läs/skriv/redigera filer, kör kommandon |
| 05 | Chat Interface | `Skill_Command_System.md` | "skill:X" → ladda + kör skill i chatten |
| 05 | Chat Interface | `Chat_Persistence.md` | Spara/återställ sessioner, export |
| 06 | Model Providers | `Provider_Architecture.md` | Abstrakt gränssnitt för modell-backends |
| 06 | Model Providers | `Built_In_Providers.md` | DeepSeek, OpenAI, Anthropic — inbyggda providers |
| 06 | Model Providers | `Custom_Provider_API.md` | Koppla in egen LLM: REST, OpenAI-kompatibel |
| 06 | Model Providers | `Local_Model_Support.md` | Ollama, llama.cpp, lokal inferens |
| 06 | Model Providers | `Provider_Configuration_UI.md` | Lägg till/ta bort/byta provider i gränssnittet |
| 07 | System Control | `Start_Stop_Pipeline.md` | Starta/pausa/stoppa hela Forge-loopen |
| 07 | System Control | `Configuration_Panel.md` | Inställningar: modeller, providers, paths, resurser |
| 07 | System Control | `Health_Monitoring.md` | CPU, GPU, RAM, disk — systemhälsa live |
| 08 | Data Layer | `Hermes_CLI_Bridge.md` | Anropa Hermes-kommandon från appen |
| 08 | Data Layer | `Real_Time_Updates.md` | Polling vs WebSocket vs IPC — strategi |
| 08 | Data Layer | `Local_Storage.md` | IndexedDB/SQLite för historik, inställningar |
| 09 | Technical Stack | `Desktop_Framework_Choice.md` | Tauri vs Electron vs Python — analys och val |
| 09 | Technical Stack | `Build_Pipeline.md` | Hur man bygger StydeForge.exe |
| 09 | Technical Stack | `Auto_Update.md` | Självuppdatering, versionshantering |
| 10 | Phase Transition | `Phase0_to_Phase1.md` | Design → implementation: scope, prioritering |

---

## Rekommenderad Läsordning

1. `DASHBOARD_INDEX.md` ← Du är här
2. `Dashboard_Vision.md` — Varför bygger vi detta?
3. `Application_Architecture.md` — Helikopterperspektiv över hela appen
4. `02_UI_UX/Layout_Design.md` — Hur ser det ut?
5. `06_Model_Provider_System/Provider_Architecture.md` — Chattens motor
6. `05_Chat_Interface/Chat_Agent_Tools.md` — Vad kan chatten göra?
7. `09_Technical_Stack/Desktop_Framework_Choice.md` — Teknikval
8. Därefter: Application Shell → Agent Monitor → Benchmark → System Control → Data Layer → Build → Phase Transition

---

## Designprinciper

| Princip | Innebörd |
|---------|----------|
| **Appen = Mission Control** | Dashboarden är hela programmet — ingen separat tjänst |
| **En knapp för allt** | Starta, pausa, stänga StydeForge från ett ställe |
| **Provider-agnostisk** | Byt modell med ett klick — DeepSeek, OpenAI, din egen |
| **Chatten är en agent** | Inte bara Q&A — den läser/skriver/redigerar filer |
| **Mörkt och kompakt** | Terminal- estetik, inget onödigt |
| **Portabelt** | En .exe — inga dependencies, ingen installation |

---

**Status:** Phase 0 — Dokumentation pågår.
**Senast uppdaterad:** 2026-06-25
