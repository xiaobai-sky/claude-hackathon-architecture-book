[ZhuTu_ZhuJi_Architecture_V5.md](https://github.com/user-attachments/files/26894818/ZhuTu_ZhuJi_Architecture_V5.md)
# ZhuTu ZhuJi (铸图铸机)
### A Human-AI Co-Creation System for Claude Code — Full Architecture Document

> **Hackathon:** Built with Opus 4.7 · Claude Code Virtual Hackathon
> **Category:** Claude Code Plugin · Agentic Workflow
> **Runtime:** Claude Code desktop, loaded as an MCP plugin
> **License:** Open Source

---

## 1. The Problem Worth Solving

Every AI planning and coding tool in existence makes the same implicit design choice: it positions the human as either a **consumer** (AI produces, human receives) or a **commander** (human directs, AI executes). Both framings misplace where human value actually lives.

Human beings are not good at answering technical questions on demand. They are exceptional at connecting concepts across domains that have no business being connected — and that cross-domain collision is precisely where genuinely novel ideas originate. An AI system that interrogates the human for technical specifications is wasting the only resource the human uniquely contributes.

ZhuTu ZhuJi is built on a different premise:

> **The answer does not exist before the conversation. It emerges inside it — and neither party holds it alone.**

This is not a productivity tool. It is a thinking environment — one where Claude and the human genuinely co-author a technical architecture, and where that architecture is then executed with deterministic precision. The planning layer and the execution layer are designed as a single, philosophically coherent system.

---

## 2. System Overview: Two Layers, One Contract

ZhuTu ZhuJi is a two-layer system that runs entirely inside Claude Code as an MCP plugin.

```
┌─────────────────────────────────────────────────────────────┐
│  ZHŪTÚ — Planning Layer  (V5.0)                              │
│                                                              │
│  Vague Idea → Structured Dialogue → Signed Blueprint JSON   │
│                                                              │
│  Information flow: AI expands possibilities →               │
│    human finds new directions → AI follows further           │
└──────────────────────────┬──────────────────────────────────┘
                           │  Signed Blueprint JSON
                           │  (one-directional, read-only)
                           ▼
┌─────────────────────────────────────────────────────────────┐
│  ZHŪJĪ — Execution Layer  (V1.5)                             │
│                                                              │
│  Blueprint → Code Generation → Validation → Self-Repair     │
│                                                              │
│  Creativity ends here. Precision is the only standard.      │
└─────────────────────────────────────────────────────────────┘
```

The contract between layers is strict and one-directional: ZhuTu's signed output is ZhuJi's only source of truth. The execution layer never rewrites the planning layer's conclusions — it can only escalate genuine architectural crises back to the human.

---

## 3. Design History: Five Generations

This system did not arrive at its current form by design. It arrived by repeatedly building something, finding what was wrong with it, and being willing to discard the parts that did not survive contact with reality. The evolution is part of the argument.

### V1.0 — Standalone Application

The original concept was a standalone desktop application: a full pipeline from raw idea to executable blueprint, with multiple AI roles orchestrated by a central controller.

**What it got right:** The two-layer separation between planning and execution. The insight that human value lives in the planning layer, not in answering technical questions. The intuition that different cognitive tasks need different role profiles.

**What it got wrong:** Everything operational. Building a standalone AI application meant managing the entire stack from scratch — API wrappers, session state, context window management, caching, UI. The engineering overhead consumed the design space.

**The lesson:** Building infrastructure is not building a product. The right move was to find an environment that had already solved the infrastructure problems and build on top of it.

### V2.0 — Heterogeneous Multi-Model Architecture

V2.0 introduced genuine model diversity: Claude for planning and synthesis, Gemini for cross-validation from a different training distribution, DeepSeek for cost-sensitive explanation tasks.

**What it got right:** The auditor role design — specifically the insight that a model which generated a blueprint cannot reliably audit its own blind spots. Cross-validation by a structurally different reasoner is a real improvement. This insight is preserved in every subsequent version.

**What it got wrong:** Operational fragility. Managing three providers meant three API credential stores, three rate limit regimes, and three failure modes. More fundamentally: the cognitive diversity was not coming from model heterogeneity. It was coming from role definition differences. A Blueprint Architect with a high-criticality system prompt and a Macro Auditor with a challenge-everything constraint are cognitively different whether they run on the same model or three different ones.

**The lesson:** Heterogeneity at the role-definition layer achieves the same cognitive diversity as heterogeneity at the model layer — without the integration tax.

### V3.0 — Single-Model Multi-Role Isolation (Claude Code Plugin)

V3.0 made the decisive architectural shift: move the entire system inside Claude Code as an MCP plugin, and replace heterogeneous models with deeply differentiated role definitions on a single model family.

**What it got right:** The plugin architecture. The local state machine. The structured JSON blueprint format. The P0/P1/P2 audit severity system with machine-verifiable PASS conditions. All retained without modification through V5.0.

**What it got wrong — three things:**

**(1) The role hierarchy was inverted.** V3.0 positioned the Blueprint Architect as the primary reasoning node. In practice, the role that engages the human in genuine dialogue — extracts precise technical requirements from ambiguous human language, generates the raw material the Architect then structures — does the harder work. The model assignments reflected this inversion, and the output quality reflected that mistake.

**(2) The Explainer role was architecturally trapped by its own name.** "Explainer" encodes a unidirectional assumption: one party knows, the other learns. This design commitment closed off genuine exploration. The role could lecture with precision but could not think alongside the human. The name was not a label — it was a behavioral contract that foreclosed collaboration.

**(3) End-of-session fusion ran as a three-step sequence.** Each intermediate step was a lossy filter. Information that appeared low-value at step one could turn out to be the critical cross-reference at step three — but by then it had already been discarded.

**The lesson:** Role names are not neutral labels. Sequential processing optimizes for process clarity at the cost of information integrity.

### V4.0 — The Thought Partner Model

V4.0 is not a patch on V3.0. It is a rethinking of the foundational relationship between the human and the system. The central change is philosophical before it is architectural: the system is no longer designed around "how do we deliver better information to the human?" It is designed around "how do we create conditions where genuine thinking happens?"

The Explainer becomes the Mentor. The in-session Blueprint Architect is deliberately downgraded to a lightweight summarizer running on Haiku 4.5. The three-step fusion sequence is collapsed into a single Opus call that holds all raw materials simultaneously. Human review is reframed from technical correctness verification to intent confirmation.

**What it got right:** The thought-partner relationship as a first principle. The spiral information flow. The Opus single-call synthesis. The append-only cache discipline. These are structurally correct and are preserved in V5.0.

**What it got wrong — two things:**

**(1) The in-session summarizer was under-powered for its actual job.** The role now called the Topic Archivist — formally the "Blueprint Architect (in-session)" — was assigned to Haiku 4.5 on the assumption that structured summarization is a mechanical task. It is not. Producing "why this was modified" and "what happens if it isn't" requires structural judgment, not just text compression. Haiku 4.5's limitations showed up in exactly these explanatory fields, where the quality of the reasoning determines whether the human can evaluate intent correctly.

**(2) The cache architecture was described in principle but never specified in implementation.** The shared prefix mechanism — the fact that all Sonnet 4.6 roles can hit the same cached token block if their input prefixes are identical — was implicit. Without explicit design, it could not be reliably maintained across role transitions, and its cost benefits could not be intentionally captured.

**The lesson:** "Lightweight" is a cost judgment, not a capability judgment. When the task has genuine structural judgment requirements, assigning a weaker model does not save money — it shifts the cost to downstream repair and human re-evaluation. The cache architecture needs to be an explicit engineered layer, not an emergent side-effect.

### V5.0 — Model Unification and Cache Formalization (Current)

V5.0 makes two structural changes to V4.0 and leaves everything else intact.

**Change 1: Haiku 4.5 is retired. All daily operational roles use Sonnet 4.6.** The Topic Archivist and Session Summarizer are upgraded from Haiku 4.5 to Sonnet 4.6. This eliminates the two-model maintenance burden, removes the quality gap in the archivist's explanatory reasoning, and introduces no material cost increase because both roles trigger infrequently and the shared cache prefix already absorbs the majority of their input token cost.

**Change 2: The cache architecture becomes an explicit, designed layer.** The shared prompt prefix structure is formalized: every Sonnet 4.6 role loads the same Unified Plan and Unified Spec block at the head of its prompt. The first role call in a session writes this block to cache; all subsequent role calls hit it at 0.1× cost. The 1-hour TTL is explicitly specified. The append-only session discipline is elevated from a cache side-effect to a named architectural principle.

---

## 4. Planning Layer — ZhuTu V5.0

### 4.1 Principle Zero: The Thought Partner Relationship

> Answers emerge in dialogue. Before the conversation, they do not exist in either party.

This principle sits above all technical decisions. Any design that positions the AI as the knower and the human as the receiver violates it. Any design that positions the human as the technical responder and the AI as the questioner also violates it.

The information flow is not a pipeline. It is a spiral:

```
Human proposes a direction
        ↓
AI expands the possibility space within that direction
        ↓
Human discovers something unexpected in the expansion
        ↓
AI follows the new direction further
        ↓
(Architecture accumulates in this spiral)
```

### 4.2 Role System

**Dual-layer model structure:** Sonnet 4.6 runs all daily operational nodes. Opus enters only for emergency circuit-breaking and end-of-session synthesis. There is no third tier.

| Role | Model | Extended Thinking | Function |
|------|-------|-------------------|----------|
| **Mentor** | Sonnet 4.6 | Enabled | Primary dialogue partner. Explores technical options, expands possibility space, captures cross-domain creative leaps. Core divergent reasoning node. |
| **Topic Archivist** | Sonnet 4.6 | Disabled | Real-time structured summarizer. Triggers every 7–10 turns or at topic boundary. Outputs JSON + Chinese annotation. Produces "why modified" and "what happens if not." Append-only. |
| **Opus Fusion Architect** | Opus | Enabled | End-of-session single-call synthesis. Holds all raw materials simultaneously. Produces the next session's cold-start document. One call; never split into steps. |
| **Auditor** | Sonnet 4.6 | Enabled | Merges macro audit, proposal evaluation, and integration testing. P0/P1/P2 severity output. PASS is binary — no gray zones. |
| **Foresight Expander** | Sonnet 4.6 | Enabled | Post-audit innovation proposals. High temperature, genuine generative depth required. Proposals never auto-write to blueprint; require Auditor evaluation then human decision. |
| **Opus Breaker** | Opus | Enabled | Emergency only. Same logical contradiction unresolved for 3 consecutive rounds → triggers. Outputs resolution directions, injected into Mentor, restarts the flow. Auto-resets on resolution. |
| **Session Summarizer** | Sonnet 4.6 | Disabled | Triggers before Opus Fusion at session end. Generates progress preview for human review. Convergent task; low-frequency trigger; cost delta versus Haiku is negligible. |

**On the Mentor's single behavioral constraint:**
*Prohibit transferring cognitive burden to the user; permit guiding the direction of their thinking.*

The Mentor may ask "which of these directions feels most important to you?" It may not ask "can you explain your technical requirements in more detail?" These questions look similar. They are structurally opposite. The first keeps the human in the role of creative initiator. The second converts the human into a technical specification machine — which is precisely the role the Mentor was designed to replace.

**On the Topic Archivist upgrade from Haiku to Sonnet 4.6:**
The archivist's responsibility is not transcription. Producing a meaningful "why this was modified" explanation — one that lets the human evaluate whether the decision matches their intent — requires structural reasoning about causality and trade-offs. Haiku 4.5's limitations appeared consistently in exactly these explanatory fields. The cost of assigning Haiku was not saved tokens; it was downstream re-evaluation work that consumed more tokens than the upgrade ever would have. The shared cache prefix means the archivist's input token cost is already absorbed at 0.1× — the upgrade cost is marginal.

**On the Opus Fusion Architect:**
This is the single most important model call in the entire planning layer. It triggers after session cache has naturally expired, receiving the current blueprint, all session patches, all high-value verbatim content, and all turn summary tags simultaneously. It produces the next session's cold-start document in one pass. This is not a cost optimization opportunity. The quality of this synthesis compounds forward as a multiplier on every subsequent session. No intermediate distillation step is introduced — every intermediate step is a lossy filter. Opus has the cross-domain synthesis capability to build lateral connections directly from raw materials. Delegating that construction to a pre-filtering step is substituting a lesser capability for a greater one.

### 4.3 Output Format: JSON + Inline Annotation

Every role that produces structured output produces it with inline annotation. The format principle is stable across all versions:

```json
{
  "module": "auth_service",
  "invariant": "token_expiry <= 3600s",
  "dependency": ["user_store", "cache_layer"]
}
```
> **Annotation:** This patch adds an expiry ceiling after the Auditor flagged an unbounded token lifetime as a P0 vulnerability. One hour balances security exposure against re-authentication friction. If longer sessions are required, the correct solution is token refresh, not a higher ceiling.

Scripts parse the JSON block. Humans read the annotation. Neither interferes with the other.

Conceptual content that resists formalization — design philosophy, trade-off reasoning, cross-domain analogies — is written directly in natural language and never forced into a JSON field where it would lose its meaning.

### 4.4 Cache Architecture

Prompt caching is the economic foundation of the planning layer. V5.0 makes this architecture explicit rather than emergent.

**Prompt layering structure — ordered from slowest-changing to fastest-changing:**

```
[Unified Plan]          ← Shared by all roles; unchanged for the lifetime of the project
[Unified Spec]          ← Shared by all roles; changes only on version iteration
[Role-Specific Spec]    ← Unique to each role; changes only on role redefinition
[Role Prompt]           ← Unique to each role; changes during tuning
```

**Cross-role cache sharing mechanism:**

All Sonnet 4.6 roles share an identical `[Unified Plan + Unified Spec]` prefix block. The first role invoked in a session writes this block to cache. Every subsequent Sonnet 4.6 role hits the same cached prefix at 0.1× cost, paying only the write cost for its own role-specific layers.

```
Mentor (first call)       → [Unified Plan + Unified Spec]  written to cache (1.25× or 2×)
                             [Mentor-specific layers]        written to cache

Topic Archivist (first)   → [Unified Plan + Unified Spec]  cache HIT  (0.1×) ✓
                             [Archivist-specific layers]     written to cache

Auditor (first call)      → [Unified Plan + Unified Spec]  cache HIT  (0.1×) ✓
                             [Auditor-specific layers]       written to cache

All subsequent calls      → Full cache HIT on all layers; pay read cost only
```

**TTL strategy — 1 hour, explicit:**

Claude Code Max subscribers: 1-hour TTL applied automatically server-side; no configuration required.

API key users: specify `"ttl": "1h"` in `cache_control`. Write cost is 2× base input price; read cost is 0.1×. Any session where the same prefix is hit more than twice within an hour recovers the write premium. Active sessions always satisfy this condition.

The 1-hour TTL is not merely convenient — it is structurally necessary. Mentor and Auditor nodes run with extended thinking enabled; single inference passes can exceed 5 minutes. A shorter TTL would expire mid-session under normal operating conditions.

**Cache invalidation avoidance — the append-only principle:**

The following operations invalidate the cache from the modification point forward:
- Modifying any file content already loaded into the session prefix
- Inserting dynamic content into prompt prefixes (timestamps, incrementing counters)
- Switching models mid-session
- Adding or removing MCP tools mid-session

Therefore: **all file operations during a session are append-only. Consolidation and merging are deferred to after "End Session," when the cache has naturally expired.** File stability equals cache hit rate.

**API key switching:**

When Claude Code subscription quota is exhausted, the user can switch to direct API billing:

```bash
export ANTHROPIC_API_KEY="your-api-key"
# Restart Claude Code to apply
```

After switching, cache is rewritten on first call. Switch during lightweight Mentor dialogue nodes rather than at Opus Fusion Architect trigger points to minimize write cost.

### 4.5 Value Tag System

Two tiers only. No compression layer.

`[VALUE:PROTECTED]` — Content whose loss would cause directional drift in the next cold-start. Captured verbatim from the raw output stream.

`[VALUE:NOTABLE]` — Content with reference value for the current module, but not directional. Captured verbatim.

**Why no compression layer:**
Compression is a lossy transformation, and the loss is unpredictable. The Opus Fusion Architect has the capability to build lateral connections across raw materials directly — it does not need pre-filtering. Every intermediate distillation step is performing work that Opus does better, at the cost of discarding information that pre-filtering judges to be low-value at that moment. The cross-reference that turns out to be critical is precisely the one that pre-filtering eliminates — because its significance was not yet apparent.

**Annotation standard:**
The Mentor annotates conservatively. The test for annotation is: *If this content were re-read three months from now, would it still let the reader reconstruct the reasoning behind the decision?* That is the threshold. Not importance in the current moment — durability of decision logic.

**Script extraction:**
The value tag listener monitors the Mentor's output stream, extracts tagged content verbatim, and appends to `.context/high_value/`. No modification. No display in the conversation interface.

### 4.6 Script System

Scripts own all deterministic operations. Models own all reasoning. The boundary is enforced structurally: scripts never invoke models; models never execute scripts. The interface is structured tags that models embed in their output — scripts listen and extract.

**Lightweight scripts (run during session via Claude Code Hooks):**

`value_tag_listener.py` — Monitors for `[VALUE:PROTECTED]` and `[VALUE:NOTABLE]` tags. Extracts verbatim content. Appends to `.context/high_value/{date}_{module_id}.md`.

`summary_tag_listener.py` — Monitors for `[SUMMARY]` tags at the end of each Topic Archivist module summary. Appends one-line summaries to `.context/global_summary.md`.

**Heavy scripts (triggered at end-of-session only):**

`validate_blueprint.py` — JSON Schema validation, invariant assertion execution, dependency tree integrity verification. Correctness criteria are binary; deterministic scripts, not models, execute them.

**Custom slash commands:**

`/end-session` — Triggers the complete end-of-session flow: Session Summarizer preview → heavy script validation → Opus Fusion Architect single call → output next-session cold-start document.

`/rebuild-blueprint` — Triggers a standalone full-synthesis pass without ending the session. Independent of session state.

### 4.7 Session Flow

**Topic rhythm:**
Each topic unfolds naturally in Mentor–human dialogue. The signal to close a topic comes from the human advancing. Every 7–10 turns, or on explicit topic closure, the Topic Archivist triggers a module summary.

The summary is shown to the human, who can:
- Say nothing: silent archival; Mentor opens the next topic
- Raise a question: enters a new round of Mentor dialogue
- Flag an error: enters Auditor flow or re-summary

**Cross-module context loading:**
Each new topic opens an independent conversation. What is loaded: the current architecture book + all preceding module global summaries + current topic high-value verbatim content. Prior conversation transcripts are not loaded — they introduce context pollution without contributing precision.

**End Session (`/end-session`):**
Sequential execution: Session Summarizer generates progress preview → heavy script validation → Opus Fusion Architect single call → outputs new architecture book. The next session cold-starts from the new architecture book directly.

### 4.8 Project File Structure

```
{project_dir}/
├── CLAUDE.md                             ← Persistent cross-session context
├── .claude/
│   └── agents/
│       ├── mentor.md                     ← Mentor role definition
│       ├── archivist.md                  ← Topic Archivist role definition
│       ├── auditor.md                    ← Auditor role definition
│       ├── foresight.md                  ← Foresight Expander role definition
│       └── summarizer.md                 ← Session Summarizer role definition
├── docs/
│   ├── blueprint_current.json            ← Session baseline blueprint (read-only during session)
│   ├── blueprint_new.json                ← Written by Opus Fusion at end-of-session
│   ├── plan_current.md                   ← Human-readable current architecture book
│   └── plan_new.md                       ← Human-readable next-session cold-start document
├── patches/
│   └── {date}_{module_id}.json           ← Append-only during session
├── scripts/
│   ├── value_tag_listener.py
│   ├── summary_tag_listener.py
│   └── validate_blueprint.py
└── .context/
    ├── global_summary.md                 ← All module one-line conclusions (append-only)
    └── high_value/
        └── {date}_{module_id}.md         ← PROTECTED and NOTABLE content, verbatim
```

### 4.9 MCP Tool Interface

| Tool | Trigger | Returns |
|------|---------|---------|
| `zhutu_start` | User opens project | Session ID, current phase, maturity assessment |
| `zhutu_answer` | User submits input | Phase state, next dialogue prompt or phase artifact |
| `zhutu_get_state` | Debug / human review | Full session snapshot |
| `zhutu_record_module` | Archivist completes summary | Confirmation, archive path |
| `zhutu_run_audit` | Phase gate | P0/P1/P2 findings, PASS status |
| `zhutu_sign` | Human sign-off | Blocks if P0 unresolved; returns artifact paths on success |
| `zhutu_end_session` | `/end-session` command | Triggers Summarizer → scripts → Opus Fusion |
| `zhutu_rebuild_blueprint` | `/rebuild-blueprint` command | Standalone full-synthesis pass |

---

## 5. Execution Layer — ZhuJi V1.5

> **Core Design Philosophy:** Return *determinism* to traditional code; reserve *ambiguous reasoning* for the model. Replace reliance on model self-discipline with external physical constraints.

> **V1.5 Upgrade:** All major reasoning nodes unified to Claude Sonnet 4.6. The Static Diagnostician migrated from DeepSeek R1 to Sonnet 4.6 with extended thinking enabled, eliminating the cross-provider format adaptation layer while preserving multi-step static reasoning capability for concurrency safety, resource physics, and performance boundary analysis.

### 5.1 Role and Model Specifications

Every execution node binds to a fixed model and reasoning mode. Substitution is not permitted — behavioral consistency and cost predictability depend on it.

| Role | Model | Extended Thinking | Temperature | Responsibility |
|------|-------|-------------------|-------------|----------------|
| **Blueprint Architect** | Sonnet 4.6 | Enabled | 0.7 | Contract generation, divergent coverage of blind spots |
| **Programmer** | Sonnet 4.6 | Disabled | 0.1 | Deterministic code generation, strict contract adherence |
| **Quality Inspector** | Sonnet 4.6 | Enabled | 0.3 | Logical reasoning review, detection of latent issues |
| **Static Diagnostician** | Sonnet 4.6 | Enabled | 0.2 | Concurrency safety, resource physics, performance boundary static diagnosis |
| **Structural Validator** | Python deterministic script | — | — | Schema compliance + invariant regression |
| **Fusion Engine** | Python deterministic script | — | — | AST merge + dependency graph propagation |

**On temperature differentiation:** The Blueprint Architect needs divergent coverage of blind spots (0.7); the Quality Inspector needs convergent precise judgment (0.3). Same model, different parameters — cognitive separation achieved through configuration, not model switching.

**On the Static Diagnostician using Sonnet 4.6 with extended thinking:** Concurrency safety, memory leaks, and race conditions require multi-step derivation but not cross-domain synthesis. Sonnet 4.6 with extended thinking is sufficient for this class of structured reasoning, and shares the same prompt contract format as all other pipeline nodes — eliminating the cross-provider format adaptation layer that DeepSeek R1 required.

### 5.2 Overall Pipeline Architecture

```
[Blueprint Architect · Sonnet 4.6 · Extended Thinking · temp=0.7]
  Output: Business contract blueprint (JSON Schema + machine-executable invariants)
↓
[Programmer · Sonnet 4.6 · No Extended Thinking · temp=0.1]
  Output: Patch with AST composite positioning (structural keys only, no content_hash)
↓
Python Fusion Engine (deterministic, with golden-file test suite)
  ├─ content_hash self-computed and registry-verified (never trusts external values)
  ├─ Anonymous node stable ID synthesis: (file, parent_node_id, child_index)
  ├─ rollback_order auto-derived from dependency graph (not specified by Programmer)
  ├─ Pre-fusion AST static pre-scan (allowlist mode)
  └─ Dependency graph propagation: full recursion + distance-tiered warnings + high-fanout broadcast suppression
↓
Local Checker (Parser / Linter / AST Validation)
  ├─ Failure → Return + attribution tag (with structured location info)
  └─ Pass ↓
Automated Structural Validator (Python, millisecond-level)
  ├─ Contract JSON Schema compliance check
  ├─ Contract invariant fixed test set (runs every cycle, prevents semantic drift)
  ├─ Sandbox Level 1 probe
  ├─ Failure → STRUCT_FAIL + minimal repair hint (no Quality Inspector tokens consumed)
  └─ Pass ↓
[Quality Inspector · Sonnet 4.6 · Extended Thinking · temp=0.3]
  ├─ Input includes soft-failure module distance-tiered warning tags
  ├─ Every 10 cycles: natural language intent regression (against current active contract)
  ├─ Failure → Programmer receives merged code + attribution tag + error summary
  └─ Pass ↓
[Static Diagnostician · Sonnet 4.6 · Extended Thinking · temp=0.2]
  Deep static reasoning (concurrency safety / resource physics / performance boundary)
  + three-segment quota sandbox sampling
↓
RCA Payload → Rubric Lifecycle Manager
  P0/P1 dual-track retirement + sandbox period gate + conflict vector synchronous detection
```

### 5.3 Phase 1 — Intelligence and Contract Layer

#### Step 1: Dimensional Division of Reconnaissance

- **Static Diagnostician covers:** Concurrency safety, resource physics (memory/connection pools), performance boundaries. Extended thinking enabled; multi-step derivation permitted.
- **Blueprint Architect covers:** API semantics, business logic, compatibility boundaries.
- **Output:** `Diagnostics_Raw_Data` (physical layer) and `Blueprint_Raw_Data` (semantic layer). Dimensions do not overlap; cross-validation signal-to-noise ratio is higher.

#### Step 2: Structured Cross-Validation

A four-dimension mandatory coverage matrix replaces count-minimum instructions, eliminating fabricated criticism from polluting the adjudication input. CLEAR findings are equally valid but must state explicit reasons. Criticism that cannot locate specific evidence is automatically tagged `UNVERIFIED` and down-weighted at adjudication.

| Dimension | Status | Evidence Location (required) |
|-----------|--------|------------------------------|
| Concurrency Safety | RISK / CLEAR | Reference specific field in counterpart's proposal |
| Memory / Resources | RISK / CLEAR | Reference specific field in counterpart's proposal |
| Compatibility | RISK / CLEAR | Reference specific field in counterpart's proposal |
| Boundary Conditions | RISK / CLEAR | Reference specific field in counterpart's proposal |

#### Step 3: Structured Adjudication Fusion

Executes a hard Rubric scoring matrix. UNDECIDED fields inherit severity by dimension:

- **P0-level UNDECIDED (concurrency safety / memory leaks / security vulnerabilities):** Directly blocks the main pipeline. Consistent with the Rubric P0 rule that P0 findings never auto-retire.
- **P1-level UNDECIDED (business logic):** Does not block the main pipeline; tagged `DEFERRED_RISK`. Must be resolved by human before entering contract in the next cycle.

```json
{
  "undecided": [{
    "dimension": "Concurrency Safety",
    "severity": "P0",
    "gate": "PIPELINE_BLOCKED",
    "diagnostician_position": "Pessimistic lock required",
    "architect_position": "Optimistic lock sufficient",
    "reason": "Load model unknown; both positions have supporting evidence"
  }]
}
```

> **Contract Semantic Lock:** Invariants are compiled at contract generation time into three classes of machine-executable artifacts — JSON Schema assertions, pytest unit test assertions, and Z3 SMT constraints (optional heavyweight). These artifacts form the Structural Validator's permanent fixed test set. Every cycle after fusion they must run; failure immediately triggers STRUCT_FAIL.

### 5.4 Phase 2 — Code Generation and Repair Layer

#### Step 4: Core Code Generator

- **Execution node:** Sonnet 4.6, temperature=0.1, extended thinking disabled.
- Output: Code draft strictly conforming to contract. No Yapping protocol enforced; placeholders prohibited.
- The Programmer provides only structural positioning keys `(file, ast_node_type, node_id)`. The `content_hash` field is left empty or omitted — computed autonomously by the Fusion Engine.
- `rollback_order` is auto-derived by the Fusion Engine from the dependency graph; the Programmer does not specify it.

#### Step 5: Python Fusion Engine

**AST Composite Key: Structural Positioning + Engine-Autonomous Hash Computation**

The `(file, ast_node_type, node_id)` triple forms a structurally unique constraint. `content_hash` is computed by the Fusion Engine from the filesystem before execution and written to the registry. External values are never trusted.

```python
# Patch submitted by Programmer (no content_hash field)
{
  "target": {
    "file": "src/webhook/handler.ts",
    "ast_node_type": "FunctionDeclaration",
    "node_id": "handleWebhook"
  },
  "action": "replace",
  "content": "..."
}

# Fusion Engine internal execution
current_hash = sha256_truncate_64(read_node_content(target))
if registry.get(node_id) != current_hash:
    return Error("STALE_BASE", current_hash, diff_summary)
```

**Anonymous Node Stable ID Synthesis**

For nodes with no explicit name (anonymous arrow functions, IIFEs, inline callback functions), the Fusion Engine generates a synthetic stable ID when building the registry, and feeds this ID back to the Programmer as the positioning reference for subsequent patches. Programmers are not permitted to self-name these nodes.

```python
def make_anonymous_id(file, parent_node_id, child_index):
    return f"{file}::{parent_node_id}::anon_{child_index}"
```

**Dependency Graph Propagation: Full Recursion + Distance-Tiered Warnings + High-Fanout Suppression**

```python
# Warning level mapping
# 1 hop (direct dependency change)  → WARNING_LEVEL = HIGH   → Inspector priority review
# 2 hops                            → WARNING_LEVEL = MEDIUM → Inspector discretionary attention
# 3 hops+                           → WARNING_LEVEL = LOW    → Log only, no active prompt

HIGH_FANOUT_THRESHOLD = 20
if out_degree(node) > HIGH_FANOUT_THRESHOLD:
    node.propagation_policy = "BROADCAST_SUPPRESS"
    publish_global_notice(f"Base-layer type change: {node.id}")
```

**Pre-Fusion AST Safety Pre-Scan (Allowlist Mode)**

```python
UNSAFE_NODE_PATTERNS = [
    ("ImportDeclaration", lambda n: n.source not in APPROVED_MODULES),
    ("CallExpression",    lambda n: n.callee in {"eval", "exec"}),
    ("MemberExpression",  lambda n: n.object in {"ctypes", "subprocess"}),
]

def prescan(patch_ast) -> ScanResult:
    for pattern_type, predicate in UNSAFE_NODE_PATTERNS:
        hits = [n for n in patch_ast.find_all(pattern_type) if predicate(n)]
        if hits:
            return ScanResult(action="ESCALATE_L2", reason=hits)
    return ScanResult(action="PASS")
```

**Local Checker Attribution Tags**

| Tag | Meaning | Attribution Info | Routing |
|-----|---------|-----------------|---------|
| `SYNTAX_ERROR` | Syntactically invalid | Specific line/column + rule | Return for rewrite |
| `INTEGRATION_CONFLICT` | Context collision | Conflict fragment + source module | Return |
| `STALE_BASE` | content_hash mismatch | Current node latest hash + change summary | Regenerate from latest anchor |
| `LOGIC_SUSPECT` | Suspicious logic | Suspicious pattern description | Escalate directly to Inspector |

**Fusion Engine Circuit Breaker**

When the golden-file test suite fails in CI, a full-pipeline circuit breaker triggers. No new patches are accepted until the engine is repaired and passes all golden-file verification.

### 5.5 Phase 3 — Cache Closure and Quality Inspection Layer

```
Merged code
  └→ Automated Structural Validator (Python, millisecond-level)
       ├─ Contract JSON Schema compliance check
       ├─ Contract invariant fixed test set (every cycle; prevents semantic drift)
       ├─ Invariant assertion execution (pytest)
       ├─ Sandbox Level 1 probe
       ├─ Failure → STRUCT_FAIL + minimal repair hint (no Inspector tokens consumed)
       └─ Pass ↓
  Quality Inspector (Sonnet 4.6 · Extended Thinking · temp=0.3)
       ├─ Hard Rubric validation
       ├─ Every 10 cycles: natural language intent regression (against current active contract snapshot)
       │   Contract change → auto-reset the 10-cycle counter
       ├─ Failure → Programmer receives merged code + attribution tag + error summary
       └─ Pass ↓
```

```json
{
  "error": "STRUCT_FAIL",
  "failed_check": "Contract JSON Schema",
  "field": "response.userId",
  "reason": "Expected string, actual number",
  "hint": "Check userId field type conversion in handleWebhook return value"
}
```

### 5.6 Phase 4 — Static Diagnosis and Sandbox Layer

**Step 7: Static Logic Diagnosis Matrix**

- **Execution node:** Sonnet 4.6, extended thinking enabled, temperature=0.2.
- **Reasoning mode:** Multi-step internal derivation permitted; diagnosis quality takes priority over response latency.
- Dimensions: concurrency safety, state transitions, resource physics (memory leaks, connection pool non-release).
- Output: Machine-readable vulnerability matrix, precise to code line, with probe confidence and feature tags.

```
Role contract:
  You are a pure static code diagnosis node.
  Scope: concurrency safety / memory and resource physics / performance boundaries.
  Prohibited: business logic suggestions, code modification, repair proposals.
  Output format: strict JSON conforming to VulnerabilityMatrix schema.
  Each finding must include: code line location, confidence (0.0–1.0), feature_tags.
  Confidence below 0.85 triggers automatic sandbox escalation.
```

**Step 8: Tiered Dynamic Sandbox**

| Level | Response Time | Isolation Method | Applicable Scenarios |
|-------|--------------|-----------------|----------------------|
| L1 | Millisecond | Host process VM/eval sandbox | Covers 90% of pure-logic errors |
| L2 | Seconds | Warm Pool container fork subprocess | Integration conflicts + unsafe-feature code |
| L3 | Full | Ephemeral container | IO / network dependency errors only |

Two independent escalation trigger paths:
- **Path A (diagnostician confidence):** `confidence < 0.85` → auto-escalate one level.
- **Path B (AST pre-scan):** Unsafe node feature hit → escalate directly to L2, without waiting for Static Diagnostician.

**Three-Segment Quota Sampling**

```
Total inspection quota = 100%

├── Guaranteed baseline pool (20%)
│     All files equally distributed, purely random, not influenced by any history

├── Risk-weighted pool (60%)
│     Allocated by leak_history + feature_tags
│     Single-file cap: 30% of risk pool
│     leak_history uses 50-cycle sliding window; auto-shrinks after historical repairs

└── Freshness pool (20%)
      Ordered by last-inspected timestamp; prioritizes least-recently-checked
      New files (< 5 cycles) go directly to top; they do not queue
```

```python
def allocate_sample(all_files, total_budget):
    guaranteed    = sample_random(all_files, budget=total_budget * 0.20)
    risk_cap      = {f: min(compute_risk(f), 0.30) for f in all_files}
    risk_sampled  = sample_weighted(all_files, weights=risk_cap,
                                   budget=total_budget * 0.60)
    freshness     = {f: float("inf") if is_new_file(f) else rounds_since_checked(f)
                    for f in all_files}
    fresh_sampled = sample_weighted(all_files, weights=freshness,
                                   budget=total_budget * 0.20)
    return deduplicate(guaranteed + risk_sampled + fresh_sampled)
```

**Missed-Detection Event Closure Trigger**

Missed-detection definition: L1 passed but L2 or L3 intercepted.

```python
if l1_passed and (l2_blocked or l3_blocked):
    for tag in feature_tags:
        leak_history_window[tag].append(current_round)
        # Retains last 50 cycles; auto-expires beyond that
```

### 5.7 Phase 5 — Rubric Lifecycle Management

**Rule Classification Metadata (P0 / P1 Dual Track)**

| Rule Level | Retirement Mechanism | Applicable Scope | Human Intervention |
|------------|---------------------|-----------------|-------------------|
| P0 | Never auto-retires; only explicit human deletion | Concurrency safety, memory leaks, security vulnerabilities | Deletion requires explicit human confirmation |
| P1 | `hit_count_recent_20 / 20 < 0.05` for 30+ cycles → enters pending-retirement queue | Business logic | Human confirmation after entering queue |

**RCA → Rubric Write: Sandbox Period Gate**

```
Same bug pattern appears repeatedly:
  1st occurrence  → enters human review queue
  2nd occurrence  → elevated priority, notification triggered
  3rd occurrence+ → enters sandbox period (WARNING mode: report only, do not intercept)
  ↓
  False-positive backtest on last 20 Quality Inspector-passed code snapshots
  ├─ False-positive rate > 10% → block write, downgrade to continuous alert, human adjudication
  └─ At least 3 hits AND 3 consecutive sandbox-period cycles with zero false positives → auto-activate
       Zero-hit cycles do not count toward the consecutive count
       (prevents low-frequency rules from activating by idle cycling)
```

**Conflict Vector Detection (Synchronous + Embedding Cache)**

```python
class RuleConflictDetector:
    def __init__(self):
        self.cache = {}  # key: (rule_id, content_hash) → embedding vector

    def check(self, new_rule, existing_rules):
        new_vec = embed(new_rule.content)
        for rule in existing_rules:
            key = (rule.id, rule.content_hash)
            if key not in self.cache:
                self.cache[key] = embed(rule.content)
            sim = cosine(new_vec, self.cache[key])
            if sim > 0.95: return ConflictResult("IDENTICAL", rule)
            if sim > 0.85: return ConflictResult("HIGH_OVERLAP", rule)
        return None

# IDENTICAL    (> 0.95): Force auto-merge
# HIGH_OVERLAP (> 0.85): Enter human confirmation queue
# SIMILAR      (> 0.75): Record association, do not block
```

---

## 6. Cost Structure

### Planning Layer — Daily Session Cost

Typical session (Mentor 30 turns + Topic Archivist 5 triggers, Sonnet 4.6):

- Unified Plan + Unified Spec prefix written once; all subsequent role calls hit cache at 0.1×
- Primary cost: Mentor output tokens, approximately 300–800 tokens per turn
- Typical daily cost: $0.10 – $0.30

### Opus Fusion Architect (once per session)

| Scenario | Input Tokens | Output Tokens | Single-Call Cost |
|----------|-------------|--------------|-----------------|
| Light session | 15,000 | 5,000 | ~$0.20 |
| Typical session | 60,000 | 12,000 | ~$0.60 |
| Heavy session | 150,000 | 25,000 | ~$1.38 |

All Fusion Architect inputs are billed at base rate (non-cached), because it triggers after natural cache expiry. This is by design: the cost arrives at a moment when the cache has already returned zero, and what it purchases is synthesis capability that compounds as a multiplier on all subsequent sessions.

### Execution Layer — Per-Cycle Cost

| Stage | Model | Cache Status | Effective Cost |
|-------|-------|-------------|----------------|
| Programmer (rollback rewrite) | Sonnet 4.6 | Hits merged code cache | One-tenth cost |
| Automated structural validation | Python script | No model tokens consumed | Near-zero |
| Quality Inspector (per-cycle review) | Sonnet 4.6 | Hits merged code cache | One-tenth cost |
| Quality Inspector (every-10-cycle intent regression) | Sonnet 4.6 | Low-frequency trigger | One-tenth (low frequency) |
| Static Diagnostician (extended thinking) | Sonnet 4.6 | Three-segment sampling, not every cycle | Controlled |
| Unmodified module sections | — | Fragment cache fully reused | Zero incremental |
| SOFT_STALE modules | — | Cache valid, with distance-tiered warnings | Zero incremental |

---

## 7. Core Design Principles (Unified System)

| # | Principle | Key Mechanism |
|---|-----------|---------------|
| 1 | Answers emerge in dialogue | The planning layer's spiral structure replaces interrogation with co-exploration |
| 2 | Prohibit transferring cognitive burden; permit guiding thinking direction | Mentor opens possibility space; it never asks for technical specifications |
| 3 | Role names are behavioral contracts | "Mentor" opens collaboration; "Explainer" closes it — naming is architecture |
| 4 | Dual-layer model structure | Sonnet 4.6 for all daily nodes; Opus only for emergency circuit-breaking and end-of-session synthesis |
| 5 | Shared cache prefix across roles | All Sonnet 4.6 roles hit the same Unified Plan + Unified Spec block; written once, read many |
| 6 | Append-only during sessions | Cache prefix stability mechanism; modified files invalidate downstream cache; appended files do not |
| 7 | No intermediate distillation | Raw materials go to Opus directly; each compression step loses what pre-filtering cannot evaluate as significant |
| 8 | Return determinism to traditional code | Merge / parse / validate / dependency graph / AST pre-scan all handled by Python deterministic code |
| 9 | content_hash computed by engine, never by model | Programmer omits hash; engine reads filesystem and self-verifies |
| 10 | Anonymous node stable ID synthesis | `(file, parent_node_id, child_index)` fed back to Programmer; self-naming prohibited |
| 11 | UNDECIDED inherits dimension severity | P0-dimension UNDECIDED directly blocks pipeline |
| 12 | Dependency propagation: full recursion + tiered warnings | No depth truncation; distance decay controls signal density; silent semantic errors eliminated |
| 13 | High-fanout node broadcast suppression | Nodes with over 20 dependents emit global notice; no warning storm triggered |
| 14 | Three-segment quota sampling | Guaranteed / risk / freshness pools on independent budgets; Matthew effect in sampling eliminated |
| 15 | Missed-detection closure definition | L1 pass + L2/L3 intercept → auto-writes to leak_history, forms a closed loop |
| 16 | Sandbox period requires minimum N hits to activate | Zero-hit cycles excluded from consecutive count; low-frequency rules cannot activate by idle cycling |
| 17 | Quality regression against current active contract | Counter resets on contract change; never regresses against an expired initial version |
| 18 | Convergence is machine-verifiable | All PASS conditions are binary judgments; no gray zones, no "basically satisfactory" |
| 19 | Human review is intent confirmation, not technical verification | Each patch includes "why modified" and "what happens if not"; human judges whether it matches project intent |
| 20 | Opus Fusion as compounding multiplier | One expensive end-of-session call produces the foundation for all subsequent sessions |

---

## 8. V4.0 → V5.0 Planning Layer Changes

| Dimension | V4.0 | V5.0 |
|-----------|------|------|
| Model layers | Haiku / Sonnet / Opus three tiers | Sonnet / Opus two tiers; Haiku retired |
| Topic Archivist model | Haiku 4.5, pure convergent summarization | Sonnet 4.6, structural judgment required |
| Session Summarizer model | Haiku 4.5 | Sonnet 4.6; low-frequency trigger, cost delta negligible |
| Cache architecture | Described in principle | Explicit layered prompt structure with cross-role shared prefix mechanism |
| TTL strategy | Not specified | 1-hour TTL with Max subscription and API key paths both documented |
| Value tags | Two tiers, implicitly extensible | Two tiers, compression layer explicitly retired, verbatim capture enforced |
| Role naming | "Blueprint Architect (in-session)" | "Topic Archivist" — avoids naming collision with ZhuJi's Blueprint Architect |
| API switching | Not designed | Environment variable switch path with cost guidance |
| Slash commands | Not designed | `/end-session`, `/rebuild-blueprint` |
| File structure | Conceptual description | Explicit `.claude/agents/` directory with sub-agent role definition files |

---

## 9. What Makes This Different

Most Claude Code plugins extend what Claude Code can *do*. ZhuTu ZhuJi is about changing what Claude Code *is* — in the context of architectural planning — from a capable tool into a genuine thinking partner, and from a thinking partner into a rigorous execution engine.

The design evolution from V1.0 to V5.0 is not a feature addition roadmap. It is a progressive clarification of what the relationship between human and AI should actually look like when the goal is not task completion but knowledge creation.

That clarification has now produced five concrete architectural commitments:

The **spiral information flow** replaces the interrogation-and-response loop. The human is not a specification machine. They are the source of cross-domain creative collisions that no training distribution can generate — and the design must protect that function, not convert it into a question-answering obligation.

The **single-call Opus synthesis** replaces sequential distillation. The significance of a piece of information is not known at the moment it is produced. Pre-filtering is substituting a judgment that cannot yet be made for the synthesis capability of a model that can reason across all raw materials simultaneously.

The **shared cache prefix** replaces per-role cache isolation. Every Sonnet 4.6 role shares the same project context block. The economic case for this is straightforward; the operational discipline it imposes — append-only during sessions, consolidation only at natural expiry — is the mechanism by which the planning layer remains coherent over days and weeks of iterative development.

The **binary PASS condition** replaces graduated quality judgments. Every audit output is PASS or FAIL; there is no "basically satisfactory." This is not rigidity — it is the condition under which a machine can reliably execute a judgment without substituting its own heuristics for the human's intent.

The **signed blueprint as the sole cross-layer contract** means that when the execution layer encounters a genuine architectural crisis it cannot resolve through self-repair, it escalates to the human — not to ZhuTu's reasoning, not to its own interpretation of prior conversation, but to the human who signed the contract. This is where the system stops and waits. The line between the two layers is also the line between where AI judgment is sufficient and where human intent is irreplaceable.

The result is a system where:
- The human brings cross-domain creative collisions that no AI training distribution can generate
- The AI brings the ability to immediately expand any direction into a structured possibility space
- The signed blueprint is genuinely co-authored — neither party could have produced it alone
- The execution layer enforces that blueprint with deterministic precision, reserving escalation to the human only for genuine architectural crises

That is the design goal. ZhuTu ZhuJi V5.0 / V1.5 is the closest we have gotten to it.

---

*ZhuTu ZhuJi · Planning Layer V5.0 · Execution Layer V1.5*
*Submitted for the Built with Opus 4.7 · Claude Code Virtual Hackathon*
*Designed through human-AI co-creation — including this document*
