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
│  ZHŪTÚ — Planning Layer                                      │
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
│  ZHŪJĪ — Execution Layer                                     │
│                                                              │
│  Blueprint → Code Generation → Validation → Self-Repair     │
│                                                              │
│  Creativity ends here. Precision is the only standard.      │
└─────────────────────────────────────────────────────────────┘
```

The contract between layers is strict and one-directional: ZhuTu's signed output is ZhuJi's only source of truth. The execution layer never rewrites the planning layer's conclusions — it can only escalate architectural crises back to the human.

---

## 3. Design History: Four Generations

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

**What it got right:** The plugin architecture. The local state machine. The structured JSON blueprint format. The P0/P1/P2 audit severity system with machine-verifiable PASS conditions. All retained in V4.0 without modification.

**What it got wrong — three things:**

**(1) The role hierarchy was inverted.** V3.0 positioned the Blueprint Architect as the primary reasoning node. In practice, the role that engages the human in genuine dialogue — extracts precise technical requirements from ambiguous human language, generates the raw material the Architect then structures — does the harder work. The model assignments reflected this inversion, and the output quality reflected that mistake.

**(2) The Explainer role was architecturally trapped by its own name.** "Explainer" encodes a unidirectional assumption: one party knows, the other learns. This design commitment closed off genuine exploration. The role could lecture with precision but could not think alongside the human. The name was not a label — it was a behavioral contract that foreclosed collaboration.

**(3) End-of-session fusion ran as a three-step sequence.** Each intermediate step was a lossy filter. Information that appeared low-value at step one could turn out to be the critical cross-reference at step three — but by then it had already been discarded.

**The lesson:** Role names are not neutral labels. Sequential processing optimizes for process clarity at the cost of information integrity.

### V4.0 — The Thought Partner Model (Current Planning Layer)

V4.0 is not a patch on V3.0. It is a rethinking of the foundational relationship between the human and the system. The central change is philosophical before it is architectural: the system is no longer designed around "how do we deliver better information to the human?" It is designed around "how do we create conditions where genuine thinking happens?"

---

## 4. Planning Layer — ZhuTu V4.0

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

| Role | Model | Function |
|------|-------|----------|
| **Mentor** | Sonnet 4.6 | Primary dialogue partner — explores technical options, expands possibility space, captures cross-domain creative leaps |
| **Blueprint Architect (in-session)** | Haiku 4.5 | Lightweight real-time summarizer — triggers every 7–10 turns, outputs JSON + inline annotation |
| **Opus Fusion Architect (end-of-day)** | Opus 4.7 | Single-call synthesis of all raw materials into next day's cold-start document |
| **Unified Auditor** | Sonnet 4.6 | Merges macro audit + proposal evaluation + integration testing into one context-switched role |
| **Foresight Expander** | Sonnet 4.6 | Post-audit innovation proposals — requires genuine generative depth |
| **Opus Breaker** | Opus 4.7 | Emergency: same contradiction unresolved for 3 rounds → break the impasse |
| **Session Summarizer** | Haiku 4.5 | End-of-session progress recap — convergent task, creativity is harmful here |

**On the Mentor:** One rule replaces four prohibitions from V3.0:
*Prohibit transferring cognitive burden to the user; permit guiding the direction of their thinking.*

The Mentor may ask "which of these directions excites you?" It may not ask "can you explain your technical requirements?" These questions look similar. They are structurally opposite. The first keeps the human in the role of creative initiator. The second converts the human into a technical specification machine.

**On the Blueprint Architect:** Deliberately downgraded from V3.0's primary reasoning node. Its cognitive task is low-complexity: structured summarization of conclusions already reached in dialogue. Haiku 4.5 is sufficient. The upgrade budget was spent on the Mentor, where it belongs.

**On the Opus Fusion Architect:** The single most important model call in the entire session. Triggers after cache expiry. Receives all raw materials simultaneously — current blueprint, all session patches, complete high-value content archive, all turn summaries — and produces the next day's cold-start document in a single pass. This is why the call uses Opus 4.7: the quality of this synthesis determines the quality of every subsequent session. One expensive call compounds forward as a multiplier on all future work.

### 4.3 Output Format: JSON + Inline Annotation

V4.0 eliminates the translation layer that separated JSON production from natural language rendering in V3.0. Every role that produces structured output produces it with inline annotation:

```json
{
  "module": "auth_service",
  "invariant": "token_expiry <= 3600s",
  "dependency": ["user_store", "cache_layer"]
}
```
> **Annotation:** This patch adds an expiry ceiling after the Auditor flagged an unbounded token lifetime as a P0 vulnerability. One hour balances security exposure against re-authentication friction. If longer sessions are required, the correct solution is token refresh, not a higher ceiling.

Scripts parse the JSON block. Humans read the annotation. Neither interferes with the other.

**The format principle:** JSON for what machines need to process; natural language for what humans need to understand. Conceptual content that resists formalization — design philosophy, trade-off reasoning, cross-domain analogies — is written directly in natural language and never forced into a JSON field where it would lose its meaning.

### 4.4 Script System

Scripts own all deterministic operations. Models own all reasoning. The boundary is enforced structurally: scripts never invoke models; models never execute scripts. The interface between them is structured tags that models embed in output — scripts listen and extract, passively.

**Lightweight scripts (append-only during sessions):**
- `value_tag_listener.py` — monitors output for `[VALUE:PROTECTED]` and `[VALUE:NOTABLE]` tags, extracts content verbatim without compression, appends to `.context/high_value/`
- `summary_tag_listener.py` — monitors for `[SUMMARY]` tags in Architect module outputs, appends one-line summaries to `.context/global_summary.md`

**Heavy scripts (triggered at end-of-session only):**
- `validate_blueprint.py` — JSON Schema validation, invariant assertion checking, dependency tree integrity

The append-only constraint during sessions is not stylistic. It is the mechanism that maintains cache prefix stability. A modified file invalidates everything downstream in the cache. An appended file does not disturb the existing prefix.

### 4.5 Cache Architecture

Prompt caching is the economic foundation of the planning layer.

**Session cache strategy:** Files loaded at session start are never modified during the session. Patches exist only in conversation context. The blueprint JSON on disk does not change until "End Today's Thinking."

This means:
- Cache prefix remains identical throughout the session
- Every sub-agent invocation reloads the same files and hits the cache
- Only the current conversation content is new tokens in each turn
- Opus Fusion runs after cache expiry — zero invalidation cost

"End Today's Thinking" is the zero-cost consolidation point by design: the cache is gone, all raw materials are available, and one Opus call produces the foundation for the next session.

### 4.6 Dual-Mode Operation

```json
{
  "mode": "subscription",
  "api_key": "",
  "model_routes": {
    "mentor": "claude-sonnet-4-6",
    "auditor": "claude-sonnet-4-6",
    "fusion": "claude-opus-4-7",
    "architect": "claude-haiku-4-5",
    "summarizer": "claude-haiku-4-5"
  }
}
```

**Subscription Mode:** The plugin provides tools and manages files. All role reasoning is delegated to Claude Code's native sub-agent system through constructed prompts — Claude Code does the reasoning against the subscriber's existing quota. Zero additional API cost.

**API Mode:** The plugin makes direct calls to the Anthropic API. Full control over per-role model selection, parameter tuning, and call timing. Enables the full model routing table above.

**API Fine-Grained Mode:** Per-role model override, including non-Anthropic providers where capability gaps exist.

All three modes share identical role logic, file management, and script systems. Mode switching changes only the model invocation layer — a single abstraction function that every role calls. The cost of switching is one config field.

### 4.7 Project File Structure

```
{project_dir}/
├── CLAUDE.md                          ← Persistent cross-session context
├── docs/
│   ├── blueprint_current.json         ← Read-only during session
│   ├── blueprint_new.json             ← Written by Opus Fusion at end-of-day
│   ├── plan_current.md                ← Human-readable current architecture book
│   └── plan_new.md                    ← Human-readable next-day cold-start document
├── patches/
│   └── {date}_{module_id}.json        ← Append-only during session
├── scripts/
│   ├── value_tag_listener.py
│   ├── summary_tag_listener.py
│   └── validate_blueprint.py
└── .context/
    ├── global_summary.md              ← One-line module conclusions (append-only)
    └── high_value/
        └── {date}_{module_id}.md      ← PROTECTED and NOTABLE content, verbatim
```

### 4.8 MCP Tool Interface

| Tool | Trigger | Returns |
|------|---------|---------|
| `zhutu_start` | User opens project | Session ID, current phase, maturity assessment |
| `zhutu_answer` | User submits input | Phase state, next dialogue prompt or phase artifact |
| `zhutu_get_state` | Debug / human review | Full session snapshot |
| `zhutu_record_module` | Architect completes summary | Confirmation, archive path |
| `zhutu_run_audit` | Phase gate | P0/P1/P2 findings, PASS status |
| `zhutu_sign` | Human sign-off | Blocks if P0 unresolved; returns artifact paths on success |
| `zhutu_end_session` | "End Today's Thinking" | Triggers summarizer → scripts → Opus Fusion |
| `zhutu_reconstruct` | "Blueprint Reconstruct" button | Mid-session full Opus synthesis on demand |

---

## 5. Execution Layer — ZhuJi V1.5

> **Core Design Philosophy:** Return *determinism* to traditional code; reserve *ambiguous reasoning* for the model. Replace reliance on model self-discipline with external physical constraints.

> **V1.5 Upgrade:** All major reasoning nodes unified to Claude Sonnet 4.6. The Static Diagnostician migrated from DeepSeek R1 to Sonnet 4.6 with extended thinking enabled, eliminating the cross-provider format adaptation layer while preserving multi-step static reasoning capability for concurrency safety, resource physics, and performance boundary analysis.

### 5.1 Role and Model Specifications

Every execution node in the pipeline binds to a fixed model and reasoning mode. Substitution is not permitted — behavioral consistency and cost predictability depend on it.

| Role | Model | Extended Thinking | Temperature | Responsibility |
|------|-------|-------------------|-------------|----------------|
| **Blueprint Architect** | Sonnet 4.6 | Enabled | 0.7 | Contract generation, divergent coverage of blind spots |
| **Programmer** | Sonnet 4.6 | Disabled | 0.1 | Deterministic code generation, strict contract adherence |
| **Quality Inspector** | Sonnet 4.6 | Enabled | 0.3 | Logical reasoning review, detection of latent issues |
| **Static Diagnostician** | Sonnet 4.6 | Enabled | 0.2 | Concurrency safety, resource physics, performance boundary static diagnosis |
| **Structural Validator** | Python deterministic script | — | — | Schema compliance + invariant regression |
| **Fusion Engine** | Python deterministic script | — | — | AST merge + dependency graph propagation |

**On temperature differentiation:** The Blueprint Architect needs divergent coverage of blind spots (0.7); the Quality Inspector needs convergent precise judgment (0.3). Same model, different parameters — this embodies the principle of "identical model, differentiated role" as a means of cognitive separation.

**On the Programmer using Sonnet 4.6:** Although the Programmer's task is deterministic (temperature=0.1, strict contract adherence), code quality is the upstream of the entire pipeline. Structural defects in output are amplified during inspection, increasing total token consumption. Sonnet 4.6's superior semantic understanding and contract adherence reduces downstream repair costs.

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
  Deep static reasoning (concurrency safety / resource physics / performance boundary) + three-segment quota sandbox sampling
↓
RCA Payload → Rubric Lifecycle Manager
  P0/P1 dual-track retirement + sandbox period gate + conflict vector synchronous detection
```

### 5.3 Phase 1 — Intelligence and Contract Layer

#### Step 1: Dimensional Division of Reconnaissance

- **Static Diagnostician covers:** Concurrency safety, resource physics (memory/connection pools), performance boundaries. Extended thinking enabled; multi-step derivation permitted; immediate output not required.
- **Blueprint Architect covers:** API semantics, business logic, compatibility boundaries.
- **Output:** `Diagnostics_Raw_Data` (physical layer) and `Blueprint_Raw_Data` (semantic layer). Dimensions do not overlap; cross-validation signal-to-noise ratio is higher.

#### Step 2: Structured Cross-Validation

A four-dimension mandatory coverage matrix replaces count-minimum instructions, eliminating the problem of fabricated criticism polluting the adjudication input. CLEAR findings are equally valid but must state explicit reasons. Criticism that cannot locate specific evidence is automatically tagged `UNVERIFIED` and down-weighted at the adjudication stage.

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

The `(file, ast_node_type, node_id)` triple forms a structurally unique constraint. `content_hash` is computed autonomously by the Fusion Engine before execution, by reading the current node content from the filesystem and writing it to the registry. External values are never trusted.

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

For nodes with no explicit name (anonymous arrow functions, IIFEs, inline callback functions), the Fusion Engine generates a synthetic stable ID when building the registry, and feeds this ID back to the Programmer as the positioning reference for subsequent patches. Programmers are not permitted to name these themselves.

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
    publish_global_notice(f"Base-type layer change: {node.id}")
```

**Pre-Fusion AST Security Pre-Scan (Allowlist Mode)**

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

**Local Checker Failure Attribution Tags**

| Tag | Meaning | Attribution Info | Routing |
|-----|---------|-----------------|---------|
| `SYNTAX_ERROR` | Syntactically invalid | Specific line/column + syntax rule | Return for rewrite |
| `INTEGRATION_CONFLICT` | Context collision | Conflicting fragment + source module | Return |
| `STALE_BASE` | content_hash mismatch | Current node latest hash + change summary | Regenerate from latest anchor |
| `LOGIC_SUSPECT` | Logic suspicious | Suspicious pattern description | Escalate directly to Inspector |

**Fusion Engine Circuit Breaker**

When the golden-file test suite fails in CI, a full-pipeline circuit breaker triggers: no new patches enter the queue until the engine is repaired and all golden-file tests pass.

### 5.5 Phase 3 — Cache Loop and Quality Inspection Layer

#### Step 6: Automated Structural Validator + Quality Inspector (Separated Responsibilities)

```
Merged Code
  └→ Automated Structural Validator (Python, millisecond-level)
       ├─ Contract JSON Schema compliance check
       ├─ Contract invariant fixed test set (every cycle, prevents semantic drift)
       ├─ Invariant assertion execution (pytest)
       ├─ Sandbox Level 1 probe
       ├─ Failure → STRUCT_FAIL + minimal repair hint (no Inspector tokens consumed)
       └─ Pass ↓
  Quality Inspector (Sonnet 4.6 · Extended Thinking · temp=0.3)
       ├─ Execute Rubric hard validation
       ├─ Every 10 cycles: natural language intent regression (against current active contract snapshot)
       │   Contract change auto-resets the 10-cycle counter
       ├─ Failure → Programmer receives merged code + attribution tag + error summary
       └─ Pass ↓
```

Sample `STRUCT_FAIL` payload:
```json
{
  "error": "STRUCT_FAIL",
  "failed_check": "Contract JSON Schema",
  "field": "response.userId",
  "reason": "Expected string, actual number",
  "hint": "Check userId field type conversion in handleWebhook return value"
}
```

### 5.6 Phase 4 — Static Diagnostics and Sandbox Layer

#### Step 7: Static Logic Diagnostics Matrix

- **Execution node:** Sonnet 4.6, extended thinking enabled, temperature=0.2.
- **Reasoning mode:** Multi-step internal derivation permitted; diagnostic quality takes priority over response latency.
- Dimensions: concurrency safety, state transition flows, resource physics (memory leaks, connection pool non-release).
- Output: Machine-readable vulnerability matrix, precisely localized to code line, with probe confidence and feature tags.

**Static Diagnostician Prompt Contract (key points):**

```
You are a pure static code diagnostic node.
Scope: concurrency safety / memory and resource physics / performance boundaries.
Prohibited: business logic suggestions, code modification, repair proposals.
Output format: strict JSON conforming to VulnerabilityMatrix schema.
Every finding must include: code line location, confidence (0.0–1.0), feature_tags.
Findings with confidence below 0.85 automatically trigger sandbox upgrade.
```

#### Step 8: Tiered Dynamic Sandbox

| Level | Response Time | Isolation Method | Applicable Scenarios |
|-------|--------------|-----------------|---------------------|
| L1 | Millisecond | Host process VM/eval sandbox | Covers ~90% of pure logic errors |
| L2 | Second | Warm Pool container fork subprocess | Integration conflicts + code with unsafe features |
| L3 | Full | Ephemeral container | IO/network dependency errors only |

**Two independent sandbox upgrade trigger paths:**

- **Path A (Diagnostician confidence):** `confidence < 0.85` → automatically upgrade one level.
- **Path B (AST pre-scan):** Hits unsafe node features → directly upgrade to L2, without waiting for Static Diagnostician.

#### Three-Segment Quota Sampling

```
Total Sampling Quota = 100%

├── Guaranteed Baseline Pool (20%)
│     All files sampled equally, purely random, unaffected by any historical data

├── Risk-Weighted Pool (60%)
│     Allocated by leak_history + feature_tags
│     Single-file cap: 30% of risk pool
│     leak_history uses 50-cycle sliding window; auto-shrinks after historical repairs

└── Freshness Pool (20%)
      Sorted by last-sampled timestamp; prioritizes longest-unchecked files
      New files (< 5 cycles) placed at top of queue, skip waiting
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

def compute_risk(file):
    rate = BASE_RATE  # 0.05
    for tag in file.feature_tags:
        if tag in HIGH_RISK_FEATURES:
            rate = max(rate, 0.25)
        recent_leaks = count_recent(leak_history_window[tag], window=50)
        if recent_leaks > 0:
            rate = max(rate, 0.40)
    return rate
```

**Missed-Detection Event Closure Trigger:**

A missed-detection event is defined as: L1 passes, but L2 or L3 intercepts.

```python
if l1_passed and (l2_blocked or l3_blocked):
    for tag in feature_tags:
        leak_history_window[tag].append(current_round)
        # Retain last 50 cycles; auto-expire on overflow
```

### 5.7 Phase 5 — Rubric Lifecycle Management

#### Rule Tier Metadata (P0 / P1 Dual Track)

| Rule Level | Retirement Mechanism | Scope | Human Intervention |
|------------|---------------------|-------|--------------------|
| P0 | Never auto-retires; only deleted by explicit human action | Concurrency safety, memory leaks, security vulnerabilities | Manual confirmation required for deletion |
| P1 | `hit_count_recent_20 / 20 < 0.05` exceeding 30 cycles → enters pending-retirement zone | Business logic | Manual confirmation after entering pending-retirement zone |

#### RCA → Rubric Write: Sandbox Period Gate

```
Same bug pattern appears repeatedly:
  1st occurrence  → enters human review queue
  2nd occurrence  → elevated priority, notification triggered
  3rd occurrence+ → enters sandbox period (WARNING mode: report only, do not intercept)
  ↓
  False-positive backtest run on last 20 cycles of Inspector-passed code snapshots
  ├─ False-positive rate > 10% → block write, downgrade to continuous alert, human adjudication
  └─ At least 3 hits AND 3 consecutive sandbox-period cycles with zero false positives → auto-activate
       Zero-hit cycles do not count toward the consecutive count
       (prevents low-frequency rules from activating by empty-trigger cycling)
```

#### Conflict Vector Detection (Synchronous + Embedding Cache)

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

| Stage | Model | Cache Status | Effective Cost |
|-------|-------|-------------|----------------|
| Programmer (rollback rewrite) | Sonnet 4.6 | Hits merged code cache | One-tenth cost |
| Automated structural validation (STRUCT_FAIL return) | Python script | No model tokens consumed | Near-zero |
| Quality Inspector (per-cycle review) | Sonnet 4.6 | Hits merged code cache | One-tenth cost |
| Quality Inspector (every-10-cycle intent regression) | Sonnet 4.6 | Low-frequency trigger | One-tenth (low frequency) |
| Static Diagnostician (extended thinking) | Sonnet 4.6 | Three-segment sampling, not every cycle | Controlled |
| Unmodified module sections | — | Fragment cache fully reused | Zero incremental |
| SOFT_STALE modules | — | Cache valid, with distance-tiered warnings | Zero incremental |
| Opus Fusion (end-of-session) | Opus 4.7 | Runs after cache expiry | One call per session |

---

## 7. Core Design Principles (Unified System)

| # | Principle | Key Mechanism |
|---|-----------|---------------|
| 1 | Answers emerge in dialogue | The planning layer's spiral structure replaces interrogation with co-exploration |
| 2 | Role names are behavioral contracts | "Mentor" opens collaboration; "Explainer" closes it — naming is architecture |
| 3 | Return determinism to traditional code | Merge / parse / validate / dependency graph / AST pre-scan all handled by Python deterministic code |
| 4 | content_hash computed by engine, never by model | Programmer omits hash; engine reads filesystem and self-verifies |
| 5 | Anonymous node stable ID synthesis | `(file, parent_node_id, child_index)` fed back to Programmer; self-naming prohibited |
| 6 | UNDECIDED inherits dimension severity | P0-dimension UNDECIDED directly blocks pipeline, consistent with Rubric P0 principle |
| 7 | Dependency propagation: full recursion + tiered warnings | No depth truncation; distance decay controls signal density; silent semantic errors eliminated |
| 8 | High-fanout node broadcast suppression | Nodes with over 20 dependents emit global notice; no warning storm triggered |
| 9 | Three-segment quota sampling | Guaranteed / risk / freshness pools on independent budgets; Matthew effect in sampling eliminated |
| 10 | leak_history sliding window | 50-cycle window; risk pool quota auto-shrinks after historical repairs |
| 11 | Missed-detection closure definition | L1 pass + L2/L3 intercept → auto-writes to leak_history, forms a closed loop |
| 12 | Sandbox period requires minimum N hits to activate | Zero-hit cycles excluded from consecutive count; low-frequency rules cannot activate by idle cycling |
| 13 | Conflict detection synchronous + embedding cache | Rule set is small; cached latency is negligible; no async race conditions introduced |
| 14 | Quality regression against current active contract | Counter resets on contract change; never regresses against an expired initial version |
| 15 | Identical model, differentiated role | All reasoning nodes on Sonnet 4.6; cognitive separation achieved through temperature and extended thinking configuration |
| 16 | Single model family with reserved extension interface | V1.5 focused on reliable Claude-family operation; other providers accessible through unified diagnostic node interface without structural disruption |
| 17 | Append-only during sessions | Cache prefix stability mechanism; modified files invalidate downstream cache; appended files do not |
| 18 | Opus Fusion as compounding multiplier | One expensive end-of-session call produces the foundation for all subsequent sessions |

---

## 8. V3.0 → V4.0 Planning Layer Changes

| Dimension | V3.0 | V4.0 |
|-----------|------|------|
| Core philosophy | AI lectures → human decides | Answers emerge in dialogue together |
| Primary reasoning role | Blueprint Architect (Sonnet) | Mentor (Sonnet) |
| Architect role | Core reasoning node | Lightweight in-session summarizer (Haiku) |
| Explainer/Mentor | Translates JSON to natural language | Explores with human; Architect output is already human-readable |
| End-of-session fusion | 3-step sequential distillation | 1-step Opus synthesis on all raw materials |
| Output format | JSON (Architect) + translation (Explainer) | JSON + inline annotation, single output |
| Audit roles | 3 separate auditors | 1 unified auditor with context switching |
| Cache protection | Not explicitly designed | Append-only during session; consolidation deferred to expiry |
| Mode support | Single model family | Subscription / API / API fine-grained |
| Human review | Technical correctness verification | Intent confirmation — "does this match what you meant?" |

---

## 9. What Makes This Different

Most Claude Code plugins extend what Claude Code can *do*. ZhuTu ZhuJi is about changing what Claude Code *is* — in the context of architectural planning — from a capable tool into a genuine thinking partner, and from a thinking partner into a rigorous execution engine.

The design evolution from V1.0 to V4.0 is not a feature addition roadmap. It is a progressive clarification of what the relationship between human and AI should actually look like when the goal is not task completion but knowledge creation.

The result is a system where:

- The human brings cross-domain creative collisions that no AI training distribution can generate
- The AI brings the ability to immediately expand any direction into a structured possibility space
- The signed blueprint is genuinely co-authored — neither party could have produced it alone
- The execution layer enforces that blueprint with deterministic precision, reserving escalation to the human only for genuine architectural crises

That is the design goal. ZhuTu ZhuJi V4.0 / V1.5 is the closest we have gotten to it.

---

*ZhuTu ZhuJi · Planning Layer V4.0 · Execution Layer V1.5*
*Submitted for the Built with Opus 4.7 · Claude Code Virtual Hackathon*
*Designed through human-AI co-creation — including this document*
