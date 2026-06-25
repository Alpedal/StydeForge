# Dashboard Vision & Goals

**StydeForge Dashboard — Mission Control**
**Phase 0 Design Document**

---

## 1. Vad är StydeForge Dashboard?

StydeForge Dashboard är **hela programmet** — inte en separat webbsida som övervakar något annat. Det är skrivbordsapplikationen `StydeForge.exe` som användaren interagerar med.

Tre kärnfunktioner i ett enda fönster:
- **Övervaka** — se alla agenter, benchmarks, systemhälsa
- **Styra** — starta, pausa, stoppa hela Forge-systemet
- **Chatta** — en full agent som kan läsa/skriva filer, köra kommandon, använda skills

---

## 2. Varför?

**Problemet idag (utan Dashboard):**
- Agentövervakning kräver terminal-kommandon (`hermes process list`, `hermes cronjob list`)
- Ingen överblick över prestanda och kostnader över tid
- Start/stopp sker manuellt via CLI
- Ingen gemensam plats för att interagera med systemet
- Byta modell = gräva i config-filer

**Lösningen:**
En enda .exe. Dubbelklicka. Allt finns där.

---

## 3. Mål

### 3.1 Primära mål (MVP — Phase 1)

| Mål | Beskrivning | Framgångskriterium |
|-----|-------------|-------------------|
| **Agentövervakning** | Se alla aktiva/klara agenter i realtid | Listan uppdateras inom 2s efter statusändring |
| **Systemkontroll** | Starta/pausa/stoppa Forge från en knapp | En knapptryckning = hela pipelinen stannar |
| **Chatt med verktyg** | Chatta med en AI-agent som kan läsa/skriva filer | Chatten kan redigera en fil på datorn |
| **Model switching** | Byt AI-modell i chatten med dropdown | Byta från DeepSeek till OpenAI = ett klick |

### 3.2 Sekundära mål (Phase 2+)

| Mål | Beskrivning |
|-----|-------------|
| **Benchmark-vy** | Se prestanda över tid — tokens/s, kostnad, eval-resultat |
| **Custom providers** | Koppla in egen LLM via REST-endpoint |
| **Local models** | Stöd för Ollama, llama.cpp — kör lokalt |
| **System health** | CPU/GPU/RAM/Disk — live |
| **System tray** | Minimera till tray, notiser vid viktiga händelser |
| **Auto update** | Appen uppdaterar sig själv |

---

## 4. Icke-mål (vad Dashboarden INTE är)

- **Inte en IDE** — ingen kodredigerare, inget projekthanteringssystem
- **Inte en modell-tränare** — ingen fine-tuning, ingen dataset-hantering
- **Inte en webbserver** — körs lokalt, ingen fjärråtkomst (MVP)
- **Inte en ersättning för terminalen** — avancerad felsökning görs fortfarande i CLI

---

## 5. Användarflöde

```
1. Dubbelklicka StydeForge.exe
        │
2. Dashboard öppnas med 3 paneler
        │
   ┌────┴────┬────────────┬────────────┐
   │ AGENTS  │ BENCHMARKS │   CHATT    │
   │ (lista) │  (grafer)  │ (full agent)│
   └─────────┴────────────┴────────────┘
        │
3. Toppmeny: [▶ Start] [⏸ Pausa] [⏹ Stopp] [⚙ Inställningar]
        │
4. Chatten är alltid tillgänglig — höger/neder-panel
   • Fråga: "läs D:/config.yaml och optimera den"
   • Agenten läser filen, analyserar, skriver tillbaka
   • "skill:code-review på D:/projekt/"
   • Agenten kör code-review med den specifika skillen
```

---

## 6. Målgrupp

**Primär:** Pontus (utvecklare, AI-engineer)
- Vill ha kontroll över sitt agent-ekosystem
- Byter ofta modeller (DeepSeek, OpenAI, lokala)
- Använder skills intensivt
- Vill se vad som händer utan att gräva i loggar

**Sekundär:** Andra utvecklare som kör Hermes/StydeForge
- Samma behov men med egna providers och skills

---

## 7. Framgångsmätning

| Metrik | Mål |
|--------|-----|
| Tid från dubbelklick till fungerande dashboard | < 3 sekunder |
| Tid att byta modell i chatten | < 5 sekunder (välj + verifiera) |
| Antal steg för att stoppa Forge | 1 klick |
| Chattens svarshastighet (första token) | < 2 sekunder |
| Minnesanvändning (idle) | < 150 MB |
| Installationsstorlek (.exe) | < 80 MB |

---

**Status:** Phase 0 — Design
