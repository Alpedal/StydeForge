# Component Library

**StydeForge Dashboard — Mission Control**
**Phase 0 Design Document**

---

## 1. Översikt

Alla återanvändbara UI-komponenter. Byggda som Web Components eller vanilla JS — inga ramverk. Varje komponent har HTML-struktur, CSS, och JS-beteende definierat.

---

## 2. Komponentkatalog

### 2.1 `sf-button`

```html
<sf-button variant="primary" icon="▶">
  Start Forge
</sf-button>
```

| Attribut | Värden | Beskrivning |
|----------|--------|-------------|
| `variant` | `primary`, `secondary`, `danger`, `ghost` | Visuell stil |
| `size` | `sm`, `md`, `lg` | Storlek (default: md) |
| `icon` | Valfri emoji/unicode | Ikon före text |
| `disabled` | boolean | Inaktiv knapp |

### 2.2 `sf-panel`

```html
<sf-panel title="Agents" icon="📡" collapsible pinned>
  <!-- panel-innehåll -->
</sf-panel>
```

| Attribut | Värden | Beskrivning |
|----------|--------|-------------|
| `title` | string | Panel-header text |
| `icon` | string | Panel-ikon |
| `collapsible` | boolean | Kan kollapsas |
| `pinned` | boolean | Fastnålad (kan ej stängas) |

### 2.3 `sf-tabs`

```html
<sf-tabs>
  <sf-tab label="Agents" icon="📡" active>
    <!-- innehåll -->
  </sf-tab>
  <sf-tab label="Benchmarks" icon="📊">
    <!-- innehåll -->
  </sf-tab>
</sf-tabs>
```

### 2.4 `sf-status-bar`

```html
<sf-status-bar>
  <sf-status-item icon="●" value="Running" />
  <sf-status-item label="Agents" value="3" />
  <sf-status-item label="Tokens" value="12.4K" />
  <sf-status-item label="Cost" value="$0.037" />
</sf-status-bar>
```

### 2.5 `sf-agent-card`

```html
<sf-agent-card
  name="code-reviewer-v3"
  model="deepseek-v4-flash"
  status="running"
  score="87"
  tokens="4.2K"
  cost="$0.012"
  time="2m 34s">
</sf-agent-card>
```

Visuell design:
```
┌─────────────────────────────────────┐
│ ● code-reviewer-v3         87/100  │
│   deepseek-v4-flash · 2m 34s       │
│   4.2K tokens · $0.012              │
│ ████████████████████░░ 87%  ⚡45t/s│
└─────────────────────────────────────┘
```

### 2.6 `sf-chart`

```html
<sf-chart type="line" data="..." width="100%" height="200px">
</sf-chart>
```

| Attribut | Värden | Beskrivning |
|----------|--------|-------------|
| `type` | `line`, `bar`, `gauge`, `sparkline` | Typ av graf |
| `data` | JSON | Datapunkter |
| `width` | CSS-värde | Bredd |
| `height` | CSS-värde | Höjd |
| `dark` | boolean | Alltid true i vårt fall |

### 2.7 `sf-log-viewer`

```html
<sf-log-viewer lines="100" filter="ERROR" auto-scroll>
  loggtext...
</sf-log-viewer>
```

Virtuell scroll för prestanda med stora loggar.

### 2.8 `sf-modal`

```html
<sf-modal title="Stop Forge?" open>
  <p>3 agents are still running.</p>
  <sf-button variant="danger">Stop Anyway</sf-button>
  <sf-button variant="secondary">Cancel</sf-button>
</sf-modal>
```

### 2.9 `sf-toast`

```html
<sf-toast type="success" message="Agent completed!" duration="5000">
</sf-toast>
```

Flyter in från botten-höger, auto-dismiss.

---

## 3. Chatt-specifika komponenter

### 3.1 `sf-chat-message`

```html
<sf-chat-message role="user" timestamp="15:42">
  optimera min config
</sf-chat-message>

<sf-chat-message role="assistant" model="deepseek-v4-pro" tokens="342" time="1.2s">
  Jag har läst din config.yaml. Här är min analys...
</sf-chat-message>
```

### 3.2 `sf-chat-input`

```html
<sf-chat-input
  placeholder="Ask anything... /skill:name for skills"
  model-selector
  provider="deepseek"
  model="deepseek-v4-pro">
</sf-chat-input>
```

### 3.3 `sf-tool-call` (inbäddad i chatt)

```html
<sf-tool-call tool="read_file" status="running">
  Reading D:/config.yaml...
</sf-tool-call>

<sf-tool-call tool="read_file" status="done" result="..." time="0.3s">
  Read 42 lines from config.yaml
</sf-tool-call>
```

---

## 4. Provider-komponenter

### 4.1 `sf-provider-card`

```html
<sf-provider-card
  name="DeepSeek"
  models="deepseek-v4-pro, deepseek-v4-flash"
  status="connected"
  latency="120ms">
</sf-provider-card>
```

### 4.2 `sf-provider-form`

```html
<sf-provider-form mode="add">
  <!-- Formulär för att lägga till ny provider -->
  <sf-input label="Provider Name" />
  <sf-input label="API Base URL" />
  <sf-input label="API Key" type="password" />
  <sf-button variant="primary">Test Connection</sf-button>
</sf-provider-form>
```

---

## 5. Komponent-states

Varje komponent har definierade tillstånd:

| State | CSS-klass | Exempel |
|-------|-----------|---------|
| Default | — | Normalt tillstånd |
| Hover | `.hover` | Mus över |
| Active | `.active` | Klickad |
| Focus | `.focus` | Tangentbordsfokus |
| Disabled | `.disabled` | Inaktiv |
| Loading | `.loading` | Visar spinner |
| Error | `.error` | Fel-tillstånd |
| Empty | `.empty` | Ingen data |

---

## 6. Implementation

Alla komponenter implementeras som **Web Components** (Custom Elements v1):

```javascript
class SfButton extends HTMLElement {
  constructor() {
    super();
    this.attachShadow({ mode: 'open' });
  }

  connectedCallback() {
    this.render();
  }

  static get observedAttributes() {
    return ['variant', 'size', 'disabled'];
  }

  render() {
    this.shadowRoot.innerHTML = `
      <style>/* scoped CSS */</style>
      <button class="sf-button ${this.getAttribute('variant')}">
        <slot></slot>
      </button>
    `;
  }
}

customElements.define('sf-button', SfButton);
```

**Fördelar:**
- Inga ramverk — noll beroenden
- Scoped CSS — inga stil-krockar
- Native web standard — fungerar i alla WebView-miljöer
- Lätt att testa — varje komponent isolerad

---

**Status:** Phase 0 — Design
