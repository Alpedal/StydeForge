# Styde Forge v3.0 — Glossary

**"The Crucible"**
**Phase 0 — Reference**

En samlad ordlista över alla begrepp i Styde Forge-ekosystemet.
Används som referens vid läsning av alla design-dokument och vid Phase 1-implementering.

---

## A

**Agent**
En spawnad instans av en blueprint. En agent är en specialiserad AI som körs i ett isolerat `delegate_task`-anrop. Ex: `agent-code-reviewer-20260625-123000`.

**Agent ID**
Unikt ID för en spawnad agent. Format: `agent-<blueprint>-<YYYYMMDD>-<HHMMSS>`.

**Atomisk skrivning (Atomic Write)**
En skrivning som antingen slutförs helt eller inte alls. Använder temp-file → rename-mönster för att förhindra korrupta filer vid USB-frånkoppling eller crash.

**Automatic Recovery**
Mekanism som vid Forge-startup detekterar om föregående session kraschade och återställer senaste giltiga checkpoint.

**Automatic Validation**
Lager 5 i evalueringspipelinen. Automatiserade tester (static analysis, linting, converage check) som körs mot agentens output.

**Automatic Version Increment**
Komponent i meta-lagret som automatiskt höjer en blueprints version baserat på eval-delta: Major (>0.15 + arkitekturändring), Minor (>0.10), Patch (>0.01).

---

## B

**Bayesian Weight Optimization**
Lager 6 i evalueringspipelinen. Använder NUTS (Machine-A) eller VI (Machine-B) för att dynamiskt justera vikterna i rubric:en baserat på historisk prestanda.

**Benchmark**
Standardiserat test för att utvärdera en agent. Varje benchmark har en `task.md` (uppgift) och `rubric.yaml` (bedömningskriterier). 6 benchmarks finns: code-review-basic, research-basic, automation-basic, documentation-basic, testing-basic, meta-basic.

**Bias Calibration**
Lager 4 i evalueringspipelinen. Periodisk kalibrering av judges mot kända benchmarks för att detektera och korrigera systematiska bias.

**Blueprint**
En mall som definierar en agenttyp. Består av `BLUEPRINT.md` (syfte/domän), `persona.md` (röst/beteende), `config.yaml` (modellval, hårdvaruprofil), `skills/` (domänspecifika skills), och `versions/` (versionshistorik).

---

## C

**Checkpoint**
En atomär ögonblicksbild av hela forge-state vid en given tidpunkt. Innehåller `state.yaml`, alla blueprints, agenter, och eval-resultat. Används för återställning och portabilitet.

**Composite Score**
Viktad summa av self-eval och judge-eval. Formel: `self_eval.score * 0.3 + judge_eval.score * 0.5 + consensus_adjustment * 0.2`. Avgör om agenten passerar kvalitetsgränsen.

**Cross-Judge Consensus**
Lager 3 i evalueringspipelinen. Flera oberoende judges (t.ex. DeepSeek + Claude + Grok) utvärderar samma agent-output och resultaten jämförs för att identifiera avvikelser.

**Crucible, The**
Kodnamn för Styde Forge v3.0. Syftar på smältdegeln där råa blueprints förädlas till elit-agenter.

---

## D

**delegate_task**
Hermes Agent-verktyget som spawnar en subagent med specifik kontext, mål, och verktyg.

**Domain**
Kunskapsområde som en blueprint/agent är specialiserad inom. Sex domäner: Coding, Research, Automation, Documentation, Testing, Meta.

**Dual Averaging**
Adaptiv step-size-algoritm inom sampling-stacken. Används för att dynamiskt justera NUTS step-size.

---

## E

**Eval Pipeline**
Sex-lagers utvärderingssystem: 1) Self-Eval, 2) LLM-as-Judge, 3) Cross-Judge Consensus, 4) Bias Calibration, 5) Automatic Validation, 6) Bayesian Weight Optimization.

---

## F

**Forge**
Styde Forge som helhet — det självförbättrande ekosystemet av elit-agenter.

---

## G

**Generation**
En version av en agent skapad genom en loop-iteration. Varje generation har ett eval-resultat och bidrar till Historical Learning.

---

## H

**Hardware Profile**
En maskinspecifik konfiguration (pontus-main, pontus-light, pontus-beast) som definierar tillgänglig VRAM, RAM, CPU, sampling-metod, och modellval.

**Hermes Agent**
Den underliggande AI-plattformen (v0.17.0+) som Styde Forge körs ovanpå. Tillhandahåller `delegate_task`, verktyg, och API-hantering.

**Historical Learning System**
Komponent i meta-lagret. SQLite-databas som analyserar tidigare generationer, eval-resultat, och teacher-feedback för att driva kontinuerlig förbättring.

**Hook**
En trigger-baserad integration som körs vid specifika events i Forge (t.ex. pre-spawn, post-eval). Inom `03_HOOKS/`.

---

## I

**Import Strategy**
En-prompt-metoden för att importera en hel Styde Forge på en ny maskin. USB → zip → extrahera → validera → kör.

---

## J

**Judge**
En LLM-modell som utvärderar en agents output mot en rubric. Oberoende från agenten som genererade outputen.

---

## L

**Loop**
En iteration av huvudloopen: DEFINE → SPAWN → EVALUATE → IMPROVE → CHECKPOINT. Varje loop producerar en new generation av en agent.

**LLM-as-Judge**
Lager 2 i evalueringspipelinen. En oberoende LLM-modell bedömer agentens output mot rubric:en.

---

## M

**Machine-A (Beast)**
Högpresterande maskin: RTX 3090 (24GB) + RTX 3080 (10GB), 64 GB RAM. Använder NUTS sampling, max 4 parallella workers.

**Machine-B (Main)**
Standardmaskin: RTX 3080 (10GB) + RTX 3070 Ti (8GB), 32 GB RAM. Använder VI sampling, max 2 parallella workers.

**Meta-Layer**
Översta lagret i Forge-arkitekturen. Innehåller Dynamic Model Selector, Historical Learning System, Automatic Version Increment, och Self-Monitoring Health.

**Meta-Improver**
Blueprint för självförbättring. Analyserar Forge-metrics och föreslår konkreta förbättringar.

---

## N

**NUTS (No-U-Turn Sampler)**
Avancerad MCMC-samplingalgoritm. Används på Machine-A för Bayesian Weight Optimization. Ger hög precision men kräver mycket VRAM.

---

## P

**Phase 0**
Designfasen. Alla 42+ dokument skapas, arkitektur definieras, ingenting byggs.

**Phase 1**
Implementeringsfasen. Infrastruktur byggs, första loopen körs, code-reviewer spawnas och evalueras.

**Phase 2**
Optimeringsfasen. Bayesian weights, cross-judge consensus, bias calibration, alla 6 blueprints aktiva.

**Phase 3**
Autonomifasen. Multi-agent collaboration, dynamic model selector, full automatisering.

**Pontus**
Prefix på alla hårdvaruprofiler — döpt efter skaparen. Profiler: pontus-main, pontus-light, pontus-beast.

---

## Q

**Quality Gate**
Kvalitetsgränsen på 80/100. Agenter under denna gräns sparas inte till USB men deras lärdomar sparas i Historical Learning.

---

## R

**Resource Governor**
Komponent som övervakar och begränsar VRAM, RAM, disk, och CPU-användning för att förhindra resursutarmning.

**Rubric**
Bedömningskriterier för ett benchmark. Definierar dimensioner (correctness, completeness, clarity, etc.), vikter, och poängskalor.

**Run**
En enskild körning av en agent mot ett benchmark. Ett run producerar `output.md`, `self_eval.yaml`, `judge_eval.yaml`, och `eval.yaml`.

**Run ID**
Unikt ID för en agent-körning. Format: `run-<YYYYMMDD>-<HHMMSS>`.

---

## S

**Sampling Stack**
Samlingen av sampling-algoritmer: NUTS, HMC, VI, Dual Averaging, Tree Depth Optimization.

**Sandbox**
Isolerad arbetskatalog för en agent. Begränsad filesystem-åtkomst, max filstorlek, timeout.

**Self-Eval**
Lager 1 i evalueringspipelinen. Agenten utvärderar sin egen output mot rubric:en.

**Self-Monitoring Health**
Komponent i meta-lagret som kontinuerligt övervakar Forge-hälsa: uptime, felprocent, resursanvändning, förbättringstrend.

**Skill**
En domänspecifik kunskapsmodul som laddas in i en agents kontext vid spawn. Lagras som `SKILL.md` i blueprintens `skills/`-katalog.

**Spawn**
Processen att skapa en agent från en blueprint via `delegate_task`. Innebär att blueprintens persona, skills, och config laddas in i subagentens kontext.

---

## T

**Teacher**
Coach-agenten som analyserar eval-resultat och ger feedback. Extraherar framgångsrika mönster som nya skills och identifierar återkommande svagheter.

**Tree Depth Optimization**
Dynamisk justering av NUTS träddjup baserat på tillgänglig hårdvara och konvergenshastighet.

---

## V

**Variational Inference (VI)**
Approximativ bayesiansk inference-metod. Används på Machine-B istället för NUTS. Snabbare men mindre precis.

**Version**
En blueprint har en semantisk version (Major.Minor.Patch). Automatisk versionshöjning baseras på eval-delta.

---

## W

**World-Class Score**
≥ 85/100 på eval. Agenter på denna nivå används som teacher/coach-kandidater.

---

**Status:** Phase 0 — Reference document. Uppdateras löpande.
**Senast uppdaterad:** 2026-06-25
