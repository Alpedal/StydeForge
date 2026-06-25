# Design Decision Log

**Styde Forge v3.0 — "The Crucible"**
**Phase 0 — Reference**

Varje designbeslut loggas här med datum, alternativ som övervägdes, och motivering.
Förhindrar att samma diskussioner återkommer månader senare.

---

## D01 — Meta-layer över Docker Swarm

| Fält | Värde |
|------|-------|
| **Datum** | 2026-06-25 |
| **Beslut** | Använd Meta-layer (in-process) istället för Docker Swarm för orkestrering |
| **Alternativ** | Docker Swarm, Kubernetes, Ray, manuell orkestrering |
| **Motivering** | Bättre VRAM-utnyttjande på Machine-B (18 GB). Swarm overhead äter upp begränsade resurser. In-process ger lägre latens. |

---

## D02 — Quality Gate ≥ 80/100

| Fält | Värde |
|------|-------|
| **Datum** | 2026-06-25 |
| **Beslut** | Alla agenter måste nå ≥ 80/100 på eval för att sparas till USB |
| **Alternativ** | Ingen gräns (spara allt), ≥ 70/100, ≥ 90/100 |
| **Motivering** | Precis som PHASE0_COMPLETE.md säger: förhindrar att USB:t fylls med mediokra agenter. 80 är högt nog för kvalitet men inte ouppnåeligt. 90 vore för strikt i början. |

---

## D03 — VI som default på Machine-B

| Fält | Värde |
|------|-------|
| **Datum** | 2026-06-25 |
| **Beslut** | Variational Inference istället för NUTS på Machine-B (18 GB VRAM) |
| **Alternativ** | NUTS på båda maskiner, VI på båda, HMC |
| **Motivering** | Speed över precision på begränsad hårdvara. VI kräver ~40% mindre VRAM än NUTS. NUTS används på Machine-A där precision är viktigast. |

---

## D04 — Atomära skrivningar överallt

| Fält | Värde |
|------|-------|
| **Datum** | 2026-06-25 |
| **Beslut** | Alla skrivningar till USB använder atomic write (temp + rename) |
| **Alternativ** | Vanliga fil-skrivningar med flush, databas, journaling filesystem |
| **Motivering** | USB disconnect safety är högsta prioritet. Temp→rename är enkelt, portabelt, och garanterar att ingen fil blir korrupt. Inget externt beroende (databas) behövs. |

---

## D05 — JSON-lines för loggar

| Fält | Värde |
|------|-------|
| **Datum** | 2026-06-25 |
| **Beslut** | Alla loggar skrivs som JSON-lines (en JSON per rad) |
| **Alternativ** | Vanlig text, YAML, SQLite, syslog |
| **Motivering** | Maskinläsbart, utrymmeseffektivt, lätt att söka med `grep`/`jq`. Ingen databas att korrumpera. |

---

## D06 — 48 GB lagringsbudget

| Fält | Värde |
|------|-------|
| **Datum** | 2026-06-25 |
| **Beslut** | Total USB-budget: 48 GB |
| **Alternativ** | 16 GB, 32 GB, 64 GB, 128 GB |
| **Motivering** | Får plats på standard 64 GB USB med ~25% marginal. Tillräckligt för 250-350 agenter + knowledge + skills. |

---

## D07 — Per-blueprint skill loading

| Fält | Värde |
|------|-------|
| **Datum** | 2026-06-25 |
| **Beslut** | Skills laddas per blueprint, inte globalt |
| **Alternativ** | Global skill pool, shared skills directory, no skills |
| **Motivering** | Renare kontext, skarpare agent-fokus. En code-reviewer ska inte få research-synthesizer-skills i sin kontext. Detta minskar också token-användning. |

---

## D08 — 8 mänskliga oversight gates

| Fält | Värde |
|------|-------|
| **Datum** | 2026-06-25 |
| **Beslut** | 8 specifika punkter där människa granskar/godkänner |
| **Alternativ** | Full autonomi från start, 20+ gates (mikro-management), 2-3 gates |
| **Motivering** | Strategisk kontroll utan mikromanagement. Viktiga brytpunkter (blueprint creation, major version, security incidents) kräver människa, resten körs autonomt. |

---

## D09 — Single-prompt import

| Fält | Värde |
|------|-------|
| **Datum** | 2026-06-25 |
| **Beslut** | Hela Forge importeras med en enda prompt via import.zip |
| **Alternativ** | Multi-steg import, git clone, nätverks-sync |
| **Motivering** | Maximal portabilitet. En prompt, en zip, klart. Fungerar även utan internet. |

---

## D10 — DeepSeek som primär cloud-provider

| Fält | Värde |
|------|-------|
| **Datum** | 2026-06-25 |
| **Beslut** | deepseek-v4-pro som default cloud-modell för evaluering |
| **Alternativ** | Claude, Grok, GPT-4, Gemini, enbart lokala modeller |
| **Motivering** | Bäst pris/prestanda-förhållande. $0.14/$0.28 per 1M tokens vs $3/$15 för Claude. Lokala Ollama-modeller används för enklare uppgifter och kostnadsbesparing. |

---

## D11 — 6 domäner (inte fler, inte färre)

| Fält | Värde |
|------|-------|
| **Datum** | 2026-06-25 |
| **Beslut** | Exakt 6 core-domäner: Coding, Research, Automation, Documentation, Testing, Meta |
| **Alternativ** | 4 domäner, 10+ domäner, generisk agent |
| **Motivering** | Täcker de viktigaste kompetensområdena utan att sprida ut sig. Varje domän har en blueprint + benchmark. Meta-domänen möjliggör självförbättring. |

---

## D12 — Agent-isolering via sandboxes (inte shared state)

| Fält | Värde |
|------|-------|
| **Datum** | 2026-06-25 |
| **Beslut** | Agenter delar state via USB-filsystem, inte via shared memory |
| **Alternativ** | Shared mutable state, message passing, database |
| **Motivering** | Förhindrar prompt injection mellan agenter. Full spårbarhet — allt är filer på USB. Knowledge överlever individuella crash:er. |

---

## D13 — API-nycklar läses från miljövariabler, aldrig sparade i forge-filer

| Fält | Värde |
|------|-------|
| **Datum** | 2026-06-25 |
| **Beslut** | API-nycklar hanteras av Hermes env/auth-system, aldrig i forge-filer |
| **Alternativ** | .env-fil i forge-roten, krypterad fil på USB, hardcoded |
| **Motivering** | Säkerhet. Om USB:t tappas bort ska inga API-nycklar läcka. Hermes hanterar rotation och credential pooling. |

---

## D14 — 300 sekunder timeout för agent-spawn

| Fält | Värde |
|------|-------|
| **Datum** | 2026-06-25 |
| **Beslut** | Default timeout för agent-spawn är 300 sekunder |
| **Alternativ** | 60s, 120s, 600s, obegränsat |
| **Motivering** | 300s är tillräckligt för även komplexa kodgranskningar men förhindrar att en agent hänger i evighet. Konfigurerbart per blueprint. |

---

## D15 — Checkpoint-intervall 25-45 minuter

| Fält | Värde |
|------|-------|
| **Datum** | 2026-06-25 |
| **Beslut** | Automatisk checkpoint var 25:e (Machine-B) till 45:e (Machine-A) minut |
| **Alternativ** | Varje loop-iteration, var 5:e minut, endast manuellt |
| **Motivering** | Frekvent nog att dataförlust är minimal vid crash, men inte så frekvent att det stör loop-flödet. Anpassas efter hårdvara (snabbare på svagare maskiner). |

---

**Status:** Phase 0 — Living document. Uppdateras vid varje nytt designbeslut.
**Antal beslut:** 15
**Senast uppdaterad:** 2026-06-25
