# Analýza relevancie: Code Execution with MCP pre rigoróznu prácu

**Dátum analýzy:** 8. november 2025
**Analytik:** Mgr. Patrik Skovajsa
**Zdroje:**
1. Anthropic Engineering Blog: "Code Execution with MCP: Building More Efficient AI Agents" (4.11.2025)
2. Medium článok (Joe Njenga): "Anthropic Just Solved AI Agent Bloat" (7.11.2025)

---

## Klasifikácia zdrojov

| Zdroj | Graphiti skupina | Kategória | Jazyk | Compliance |
|-------|------------------|-----------|-------|------------|
| Anthropic Engineering | **rigo** | Technical documentation | [EN] | ✅ Počíta sa |
| Medium (Njenga) | **rigo-corpus** | Industry blog | [EN] | ❌ Nepočíta sa |

**Rozhodnutie:**
- Anthropic článok je autoritatívny technický zdroj od vývojárov MCP → hodný citovania v thesis
- Medium článok je popularizácia → doplnkový, pre kontext a practical validation

---

## Relevancia pre výskumné otázky

### ✅ RQ1: Ako efektívne implementovať konzultačný EA prístup cez AI asistenta? (VYSOKÁ RELEVANCIA)

**Kľúčové zistenia:**

1. **Token efficiency = Scalability konzultanta**
   - Tradičný prístup: 150 000 tokenov
   - Code execution: 2 000 tokenov (98.7% redukcia)
   - **Implikácia pre thesis:** AI EA consultant môže pracovať s oveľa viac nástrojmi súčasne (ArchiMate, BPMN, UML, Mindmaps servery) bez dosiahnutia context limits

2. **Progressive tool discovery**
   - Agent nemusí poznať všetky tools vopred
   - Môže ich objavovať podľa potreby cez filesystem exploration
   - **Implikácia:** Consultant môže mať prístup k celej Enterprise Architecture tool suite bez token overhead

3. **Cost reduction**
   - Nižšie API náklady = ekonomicky udržateľnejší consultative service
   - **Implikácia:** Reálna implementácia v organizáciách je finančne realizovateľná

**Citovateľné:**
> "Rather than loading all tool definitions upfront, agents can write code to call tools on-demand, reducing token consumption by up to 98.7%." (Jones & Kelly, 2025)

---

### ✅ RQ2: Aké sú optimálne interakčné vzory pre rôzne EA stakeholder typy? (STREDNÁ RELEVANCIA)

**Kľúčové zistenia:**

1. **Two interaction patterns:**
   - **Direct tool calling:** Vhodné pre simple workflows, 3-5 tools
   - **Code execution:** Vhodné pre complex multi-step workflows

2. **Stakeholder mapping:**
   - **C-level executives:** Simple queries → direct tool calling stačí
   - **EA architects:** Complex analysis, multiple models → code execution potrebný
   - **Developers:** Integration tasks → code execution + state persistence

**Aplikácia:**
- Consultant musí dynamicky prepínať medzi interaction modes podľa user sophistication
- Different stakeholder = different complexity = different tool invocation strategy

---

### ✅ RQ3: Ako zabezpečiť trustworthiness AI odporúčaní? (STREDNÁ RELEVANCIA)

**Kľúčové zistenia:**

1. **Privacy-preserving operations**
   > "Intermediate results stay in execution environments by default. Sensitive data can be tokenized automatically, preventing accidental exposure to models." (Jones & Kelly, 2025)

   - Citlivé EA dáta (business capabilities, organizational structure) nikdy nevstupujú do LLM context
   - Iba finálne výsledky a recommendations sa vracajú

2. **Auditability through code**
   - Agent píše explicitný kód = traceable decision-making
   - Možnosť review kódu pred execution
   - **Implikácia:** Lepšia trust pre critical architectural decisions

3. **Secure sandboxing requirement**
   - Trade-off: Vyššia bezpečnosť vyžaduje komplexnejšiu infraštruktúru
   - **Relevancia pre Component 2 (MCP Gateway):** Security layer je kritický

---

### ✅ RQ4: Kde sú hranice automatizácie EA a kedy je ľudský expert nevyhnutný? (VYSOKÁ RELEVANCIA)

**Kľúčové zistenia:**

1. **Clear boundary identification:**

| Aspekt | Automatizovateľné | Vyžaduje human expert |
|--------|-------------------|----------------------|
| Tool invocation | ✅ Code execution | ❌ |
| Data filtering | ✅ In-environment processing | ❌ |
| Multi-step workflows | ✅ Control flow constructs | ❌ |
| Architectural judgment | ❌ | ✅ Human validation needed |
| Trade-off decisions | ⚠️ AI suggests | ✅ Human decides |

2. **Implementation complexity as boundary:**
   > "Code execution requires secure sandboxing, resource limits, and monitoring—adding operational overhead compared to direct tool calls. Benefits must be weighed against implementation complexity." (Jones & Kelly, 2025)

   - **Simple tasks:** Automation straightforward
   - **Complex governance:** Human oversight essential

3. **When to use which approach:**
   - **Direct tool calling (human-like):** 3-5 tools, simple workflows
   - **Code execution (automation):** Dozens of tools, complex data processing

   **Implikácia pre thesis:**
   - Návrh hybrid consultative model: AI pre routine tasks + human expert pre strategic decisions

---

### ⚠️ RQ5: Ako merať úspešnosť EA transformácie? (NÍZKA PRIAMA RELEVANCIA)

**Zistenia:**

1. **Performance metrics príspevok:**
   - Token usage reduction: 98.7%
   - Latency improvement: Mentioned but not quantified
   - Cost reduction: Implied through token efficiency

2. **Možné metriky pre thesis:**
   - Response time pre rôzne stakeholder queries
   - Cost per consultation session
   - Number of tools accessible without performance degradation

**Nepriama relevancia:** Tieto metriky môžu byť súčasťou broader EA transformation success measurement.

---

## Mapovanie na komponenty thesis architektúry

### 🎯 Component 1: MCP Servers Layer (VYSOKÁ RELEVANCIA)

**Aplikácia:**

1. **Filesystem-based tool organization:**
   ```
   servers/
   ├── archimate/
   │   ├── createElement.ts
   │   ├── validateModel.ts
   │   └── index.ts
   ├── bpmn/
   ├── uml/
   └── mindmaps/
   ```

2. **Each MCP server as TypeScript module:**
   - ArchiMate server exports: `createElement()`, `validateModel()`, `exportToArchiJSON()`
   - Agent importuje: `import { archimate } from 'mcp-servers'`
   - **Benefit:** Lazy loading, progressive discovery

3. **Implementation recommendation:**
   - Navrhnúť MCP servery tak, aby fungovali aj pre direct tool calling aj pre code execution
   - Dual-mode support pre flexibility

---

### 🎯 Component 2: MCP Gateway (VYSOKÁ RELEVANCIA)

**Aplikácia:**

1. **Security layer enhancement:**
   - Secure code execution sandbox (CRITICAL)
   - Resource limits per execution
   - Monitoring and audit logs

2. **Integration complexity:**
   > "Trade-offs: Code execution requires secure sandboxing, resource limits, and monitoring—adding operational overhead" (Jones & Kelly, 2025)

   - Gateway musí zvládnuť oba modes: tool calling + code execution
   - Dynamic routing based on task complexity

3. **Privacy architecture:**
   - Intermediate data stays in execution environment
   - Only final results pass through gateway to AI model
   - **Relevancia pre enterprise:** GDPR/NIS2 compliance friendly

---

### 🎯 Component 3: AI Architect (VEĽMI VYSOKÁ RELEVANCIA)

**Aplikácia:**

1. **Dual invocation strategy:**
   ```python
   # Pseudo-code for consultant logic
   if task.complexity == 'simple' and task.tools_count <= 5:
       use direct_tool_calling()
   elif task.complexity == 'complex' or task.tools_count > 5:
       use code_execution()
   ```

2. **Progressive tool discovery:**
   - Consultant nemusí mať všetky ArchiMate/BPMN/UML operations v initial context
   - Browse and discover based on current stakeholder need
   - **Example:** "User asks about application portfolio → discover `archimate.listApplications()`"

3. **In-context data processing:**
   - Filter 1000s of ArchiMate elements down to relevant 10
   - Process happens in execution environment
   - Return compact recommendations to user

4. **State persistence for long-running EA tasks:**
   > "Agents save intermediate results to files for resumption and persist reusable functions as structured skills" (Jones & Kelly, 2025)

   - EA analysis často trvá viac sessions
   - Checkpoint progress, resume later
   - **Implikácia:** Better user experience for complex EA transformations

---

### 🎯 Component 4: Conversational Interface (STREDNÁ RELEVANCIA)

**Aplikácia:**

1. **Transparent execution mode:**
   - User môže vidieť, či consultant používa direct calling alebo code execution
   - Show generated code pre trust building

2. **Stakeholder-adaptive interface:**
   - Junior stakeholder: Hide complexity, show only results
   - Senior EA architect: Show code, allow review before execution

---

### 🔗 Cross-cutting Concerns

**DevSecOps:**
- Secure sandbox = DevSecOps requirement
- Continuous monitoring of code execution environments
- Resource limits = security posture

**Cloud-native architecture:**
- Code execution environments = containerized sandboxes
- Scalable execution infrastructure
- **Implikácia:** Kubernetes-based deployment model

**Knowledge graphs:**
- Not directly addressed in articles
- **Potential:** Store learned patterns from code executions as graph nodes

---

## Nové koncepty pre thesis

### 1. Hybrid Consultative Model

**Nový insight:**
Consultant by nemal používať len jeden pattern, ale **hybrid approach**:

```
User query
    ↓
Complexity analysis
    ↓
├─ Simple (3-5 tools) → Direct tool calling → Fast response
└─ Complex (>5 tools, data processing) → Code execution → Efficient processing
```

**Benefit:**
- Optimalizácia pre rôzne use cases
- Best of both worlds

---

### 2. Progressive Tool Discovery Architecture

**Nový design pattern:**
Namiesto "load all MCP servers upfront", implementovať:

1. **Tool catalog service:**
   ```
   GET /mcp/servers → List available servers
   GET /mcp/servers/archimate/tools → List ArchiMate operations
   GET /mcp/servers/archimate/tools/createElement/schema → Tool definition
   ```

2. **AI Architect workflow:**
   - Browse catalog based on task
   - Load only relevant tool definitions
   - Execute using code
   - Cache learned patterns

**Relevancia pre Component 1 + Component 2:**
- MCP servers musia podporovať introspection
- Gateway poskytuje catalog API

---

### 3. Privacy-First EA Automation

**Nový príspevok k trustworthiness (RQ3):**

Architectural principle: **"Sensitive EA data never enters LLM context"**

Implementation:
```typescript
// Code executes in sandbox, not in LLM context
const sensitiveOrgData = await archimate.getOrganizationStructure();
const anonymized = sensitiveOrgData.map(dept => ({
  id: tokenize(dept.id),
  size: dept.employeeCount > 100 ? 'large' : 'small'
}));
return anonymized; // Only this goes to LLM
```

**Benefit:**
- GDPR/NIS2 compliance
- Higher trust from organizations
- Reduced risk of data leakage

---

## Potenciálne témy pre kapitoly thesis

### Kapitola: "Optimalizácia AI Consultant Performance"

**Sekcie:**
1. Token efficiency ako business requirement
2. Code execution vs. direct tool calling: Trade-off analysis
3. Progressive tool discovery: Implementation
4. Performance benchmarks: 150k → 2k tokens case study

**Zdroje:**
- Jones & Kelly (2025) - Anthropic engineering blog [RIGO]
- Njenga (2025) - Industry validation [CORPUS]

---

### Kapitola: "Bezpečnosť a dôveryhodnosť EA Automation"

**Sekcie:**
1. Privacy-preserving code execution
2. Secure sandbox requirements
3. Audit and monitoring for critical decisions
4. Compliance with EU AI Act and NIS2

**Zdroje:**
- Jones & Kelly (2025) - Privacy architecture [RIGO]
- + GRC-ADDON sources (GDPR, NIS2, AI Act)

---

### Kapitola: "Hranice automatizácie EA procesov"

**Sekcie:**
1. Task complexity analysis framework
2. Automation suitability matrix
3. Human-AI collaboration model
4. When to use which approach (decision tree)

**Zdroje:**
- Jones & Kelly (2025) - Trade-offs discussion [RIGO]
- Njenga (2025) - Practical scenarios [CORPUS]

---

## Compliance check

### Foreign language sources

**New additions:**
- ✅ [EN] Jones & Kelly (2025) - Code Execution with MCP (RIGO)
- ⚠️ [EN] Njenga (2025) - AI Agent Bloat (CORPUS - nepočíta sa)

**Impact:**
- RIGO [EN] count: +1
- Foreign language requirement: Improved

---

### Originality

**Status:**
- Code execution with MCP = **NEW topic** (published November 2025)
- 100% original content for rigorous thesis ✅

---

## Odporúčania pre ďalší výskum

### 1. Implementačný experiment (krátky)

**Návrh:**
Vytvoriť proof-of-concept:
- ArchiMate MCP server s podporou code execution
- Simple consultant agent
- Benchmark: Direct calling vs. code execution
- Metrics: Token usage, latency, cost

**Časová náročnosť:** 2-3 týždne
**Benefit:** Empirical data pre thesis

---

### 2. Doplnenie zdrojov

**Hľadať:**
- Academic papers on code execution security (sandboxing)
- Research on AI agent efficiency optimization
- Studies on human-AI collaboration boundaries

**Pridať do RIGO:**
- Peer-reviewed sources validujúce tieto patterns
- Security best practices for code execution environments

---

### 3. Cross-check s GRC-ADDON

**Otázky:**
- Ako code execution impacts compliance (AI Act, NIS2)?
- Privacy requirements for sensitive EA data in execution environments
- Audit trail requirements pre architectural decisions

**Akcia:**
- Search rigo-grc-addon for relevant compliance frameworks
- Integrate with security chapter

---

## Záver analýzy

### Vysoká strategická relevancia ✅

Tieto články prinášajú:
1. **Nový implementation pattern** pre MCP-based agents (core contribution)
2. **Clear automation boundaries** (answers RQ4 directly)
3. **Performance optimization** enabling realistic consultative service (answers RQ1)
4. **Privacy architecture** improving trustworthiness (supports RQ3)

### Odporúčané akcie

1. ✅ Pridať Anthropic článok do **rigo** ako technical documentation
2. ✅ Pridať Medium článok do **rigo-corpus** pre industry validation
3. ✅ Aktualizovať thesis outline s novými kapitolami o efficiency
4. ⚠️ Nájsť peer-reviewed akademické zdroje validujúce tieto patterns
5. ⚠️ Zvážiť implementačný experiment pre empirical validation

### Compliance impact

- Foreign language sources: +1 [EN] (RIGO)
- Originality: 100% nový obsah (publikované 11/2025)
- Relevance: Direct contribution k 3 z 5 research questions

---

**Analytik:** Mgr. Patrik Skovajsa
**Dátum:** 8. november 2025
