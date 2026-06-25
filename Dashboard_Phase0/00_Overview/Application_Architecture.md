# Application Architecture

**StydeForge Dashboard — Mission Control**
**Phase 0 Design Document**

---

## 1. Arkitekturöversikt

```
┌──────────────────────────────────────────────────────────┐
│                    StydeForge.exe                        │
│                   (Desktop Application)                   │
│                                                          │
│  ┌────────────────────────────────────────────────────┐  │
│  │                  UI Layer (HTML/CSS/JS)             │  │
│  │  ┌───────────┐  ┌────────────┐  ┌───────────────┐  │  │
│  │  │  Agents   │  │ Benchmarks │  │    Chat       │  │  │
│  │  │  Panel    │  │   Panel    │  │    Panel      │  │  │
│  │  └─────┬─────┘  └─────┬──────┘  └───────┬───────┘  │  │
│  └────────┼──────────────┼─────────────────┼──────────┘  │
│           │              │                 │              │
│  ┌────────┴──────────────┴─────────────────┴──────────┐  │
│  │                Core Controller                      │  │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────────────┐  │  │
│  │  │Process   │  │Benchmark │  │ Chat Controller  │  │  │
│  │  │Monitor   │  │Engine    │  │                  │  │  │
│  │  └────┬─────┘  └────┬─────┘  └────────┬─────────┘  │  │
│  └───────┼─────────────┼────────────────┼────────────┘  │
│          │             │                │                │
│  ┌───────┴─────────────┴────────────────┴────────────┐  │
│  │                  Data Layer                        │  │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────────────┐ │  │
│  │  │ Hermes   │  │ Local DB │  │ Provider API     │ │  │
│  │  │ CLI      │  │(IndexedDB│  │ Layer            │ │  │
│  │  │ Bridge   │  │ /SQLite) │  │                  │ │  │
│  │  └──────────┘  └──────────┘  └──────────────────┘ │  │
│  └────────────────────────────────────────────────────┘  │
│                                                          │
│  ┌────────────────────────────────────────────────────┐  │
│  │              Desktop Shell (Tauri)                  │  │
│  │  • Window management    • System tray              │  │
│  │  • Native file dialogs  • Auto-start               │  │
│  │  • Process spawning     • File system access       │  │
│  └────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────┘
         │                 │                  │
         ▼                 ▼                  ▼
┌──────────────┐  ┌──────────────┐  ┌──────────────────┐
│ Hermes CLI   │  │ File System  │  │ AI Provider APIs │
│ (hermes ...) │  │ (D:/, /logs) │  │ (DeepSeek,       │
│              │  │              │  │  OpenAI, Custom)  │
└──────────────┘  └──────────────┘  └──────────────────┘
```

---

## 2. Lager

### 2.1 Desktop Shell (Tauri)

Ansvarar för:
- Skapa applikationsfönstret
- System tray-integration (minimera, notiser)
- Starta/stoppa Hermes som child-process
- Native-funktioner: filsystem, fildialoger, auto-start
- Låg resursförbrukning (Rust-backend, ~5-10MB)

### 2.2 UI Layer (HTML/CSS/JS)

Tre huvudpaneler i ett responsivt grid:
- **Agent Panel** (vänster 30%) — agentlista, status, detaljer
- **Benchmark Panel** (mitten/höger 35%) — grafer, prestandadata
- **Chat Panel** (höger/neder 35%) — chatt med full agent

### 2.3 Core Controller

Business logic:
- **Process Monitor** — pollar Hermes CLI, håller agentlistan synkad
- **Benchmark Engine** — samlar prestandadata, beräknar grafer
- **Chat Controller** — hanterar provider-val, verktygsanrop, streaming

### 2.4 Data Layer

- **Hermes CLI Bridge** — anropar `hermes process list`, `hermes cronjob list` etc.
- **Local DB** — sparar historik (agenthistorik, chattloggar, benchmarks)
- **Provider API Layer** — abstrakt gränssnitt mot AI-modeller

---

## 3. Teknikstack (preliminär)

| Lager | Teknik | Varför |
|-------|--------|--------|
| Desktop Shell | **Tauri v2** (Rust) | Liten .exe (~5MB), native prestanda, minnessnål |
| UI Rendering | HTML/CSS/JS via Tauri WebView | Full flexibilitet, lätt att styla |
| Grafer | Chart.js (lättviktigt) | Bra prestanda, mörkt tema-stöd |
| Chatt-rendering | Markdown + syntax highlighting | Kodsnuttar, tabeller, fetstil |
| Lokal databas | IndexedDB (MVP) → SQLite (Phase 2) | Inga serverberoenden |
| Process-kommunikation | Tauri Command API (Rust ↔ JS) | Säker IPC, typad |
| Byggsystem | Tauri CLI + GitHub Actions | Bygg .exe för Windows |

---

## 4. Kommunikationsflöden

### 4.1 Agentövervakning

```
UI ──→ Core Controller ──→ Hermes CLI Bridge ──→ hermes process list
                                                      │
UI ←── Core Controller ←── Hermes CLI Bridge ←───────┘
(poll var 2:a sekund)
```

### 4.2 Chatt med verktyg

```
Användare: "läs D:/config.yaml"
        │
Chat Controller:
  1. Skicka till vald AI Provider (DeepSeek/OpenAI/etc.)
     med tool definitions
  2. Provider svarar: tool_call: read_file("D:/config.yaml")
  3. Chat Controller exekverar via Tauri filesystem API
  4. Resultat skickas tillbaka till provider
  5. Provider formulerar svar
  6. Streamas till UI (markdown)
```

### 4.3 Systemkontroll

```
UI [▶ Start] ──→ Core Controller ──→ Tauri spawn process
                                        │
                              ┌─────────┴──────────┐
                              │ Hermes process     │
                              │ (Forge loop)       │
                              └────────────────────┘

UI [⏹ Stopp] ──→ Core Controller ──→ kill process
                              ┌─────────┴──────────┐
                              │ Graceful shutdown  │
                              │ → spara checkpoints│
                              │ → stäng cron-jobb  │
                              └────────────────────┘
```

---

## 5. Säkerhetsmodell

| Funktion | Risk | Mitigering |
|----------|------|------------|
| Chatten kör terminalkommandon | Kör skadliga kommandon | Bekräftelsedialog för alla kommandon, sandbox-läge |
| Chatten skriver filer | Skriver över viktiga filer | Visa diff innan write, `--dry-run` först |
| Chatten läser filer | Läser känslig data | Ingen mitigering — användarens ansvar (lokal app) |
| Custom providers | API-nycklar lagras | Krypterad lokal lagring (operativsystemets nyckelring) |

---

## 6. Konfiguration

`StydeForge.exe` läser sin config från:
```
%APPDATA%/StydeForge/config.json
```

```json
{
  "hermes_path": "C:/Users/Pontus/.hermes/",
  "providers": {
    "deepseek": {
      "api_key": "sk-...",
      "default_model": "deepseek-v4-pro"
    },
    "openai": {
      "api_key": "sk-...",
      "default_model": "gpt-4o"
    }
  },
  "ui": {
    "theme": "dark",
    "font_size": 14,
    "start_minimized": false
  },
  "forge": {
    "auto_start": false,
    "stop_on_exit": true
  }
}
```

---

**Status:** Phase 0 — Design
