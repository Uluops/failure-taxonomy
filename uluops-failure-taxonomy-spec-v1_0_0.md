# UluOps Failure Taxonomy Specification

**Version:** 1.0.0  
**Status:** Draft  
**Created:** 2026-01-09  
**Updated:** 2026-04-08  
**Supersedes:** Failure Taxonomy Spec v0.2.2  
**Domain:** UluOps Validation & Analysis Framework  
**Patent Status:** Provisional patent filed (see §10)

---

## Table of Contents

1. [Abstract](#1-abstract)
2. [Overview & Motivation](#2-overview--motivation)
3. [Design Principles](#3-design-principles)
4. [Taxonomy Structure](#4-taxonomy-structure)
   - 4.1 [Level 1: Failure Domains](#41-level-1-failure-domains)
   - 4.2 [Level 2: Failure Modes](#42-level-2-failure-modes)
   - 4.3 [Level 3: Severity](#43-level-3-severity)
   - 4.4 [Compound Classification Code](#44-compound-classification-code)
5. [Canonical Representation](#5-canonical-representation)
6. [Classification Guidelines](#6-classification-guidelines)
   - 6.1 [Domain Selection Decision Tree](#61-domain-selection-decision-tree)
   - 6.2 [Mode Disambiguation](#62-mode-disambiguation)
   - 6.3 [Severity Selection Criteria](#63-severity-selection-criteria)
   - 6.4 [Multi-Classification](#64-multi-classification)
7. [ADL Schema Integration](#7-adl-schema-integration)
   - 7.1 [Schema Definitions](#71-schema-definitions)
   - 7.2 [Classification Strategies](#72-classification-strategies)
   - 7.3 [Forward Compatibility](#73-forward-compatibility)
8. [Validation Tracker Integration](#8-validation-tracker-integration)
   - 8.1 [Database Schema](#81-database-schema)
   - 8.2 [Validation Rules](#82-validation-rules)
   - 8.3 [Query Patterns](#83-query-patterns)
9. [Example Agent with Taxonomy](#9-example-agent-with-taxonomy)
10. [Novelty & Patent Claims](#10-novelty--patent-claims)
11. [Resolved Design Decisions](#11-resolved-design-decisions)
12. [Full Code Reference](#12-full-code-reference)
13. [Changelog](#13-changelog)

---

## 1. Abstract

This specification defines the **UluOps Failure Taxonomy**, a structured classification system for judgment failures detected by AI validation agents. The taxonomy classifies failures across four orthogonal domains — Structural, Semantic, Pragmatic, and Epistemic — with twenty-four distinct failure modes. Each failure mode is identified by a collision-free, fully-qualified code that encodes both domain and mode in a single token. The taxonomy is designed to be domain-portable (applicable to software, documents, prompts, specifications, and any artifact subject to quality judgment), runtime-classifiable (failures may be classified at detection time by agents, classifiers, or humans), and forward-compatible (new modes can be added without schema changes).

The system is novel in that it applies a linguistically-grounded failure ontology — drawn from the structural/semantic/pragmatic/epistemic hierarchy of language analysis — to the domain of AI-driven artifact validation, enabling systematic pattern recognition, blind-spot detection, and recursive self-improvement across heterogeneous agent populations.

---

## 2. Overview & Motivation

### 2.1 Problem Statement

AI validation agents detect quality issues in artifacts, but without a shared classification system, findings are unstructured text. This makes it impossible to:

- Compare findings across agents, runs, or projects
- Detect systematic blind spots in individual agents
- Track whether quality profiles improve over time
- Route findings to appropriate remediation strategies
- Measure the epistemic reliability of agents themselves

### 2.2 What the Taxonomy Classifies

Unlike traditional bug taxonomies that classify defects in artifacts, this taxonomy classifies **the gap between what an artifact should be and what it is** — the delta that expert judgment would catch. Each classification answers two questions:

1. **What aspect of quality failed?** (Domain: structural, semantic, pragmatic, or epistemic)
2. **What specific failure pattern occurred?** (Mode: the particular way quality broke down within that domain)

### 2.3 Purpose

1. **Pattern Recognition**: Identify systematic weaknesses across projects and agents
2. **Agent Development**: Target new agents at underrepresented failure modes
3. **Trend Analysis**: Track whether failure profiles improve over time
4. **Meta-Validation**: Ensure agents themselves don't have blind spots
5. **Cognitive Parallax Analysis**: Measure overlap and complementarity across agents operating through different cognitive lenses
6. **Recursive Appreciation**: Enable self-evaluating systems to characterize the nature of their own blind spots (see: Gödelian convergence boundary at ~80% effectiveness)

---

## 3. Design Principles

| Principle | Description |
|-----------|-------------|
| **Additive** | Existing agents continue working; taxonomy is optional enrichment |
| **Runtime-Classifiable** | Failures can be classified when detected, not just at definition time |
| **Queryable** | Classification enables SQL-based analysis in the UluOps Validation Tracker |
| **Domain-Portable** | Same taxonomy works across software, documents, prompts, specifications, agent definitions, and any artifact subject to quality judgment |
| **Collision-Free** | All mode codes include domain prefix for unambiguous identification (e.g., `STR-INC` ≠ `SEM-INC`) |
| **Forward-Compatible** | Schema uses pattern validation, allowing taxonomy extension without breaking changes |
| **Linguistically Grounded** | Domain structure derives from the hierarchy of language analysis: form (structural), meaning (semantic), contextual fit (pragmatic), and evidential status (epistemic) |
| **Compositional** | Compound codes (`{DOMAIN}-{MODE}/{SEVERITY}`) encode complete classification in a single, parseable token |

---

## 4. Taxonomy Structure

### 4.1 Level 1: Failure Domains

The broadest classification — what *aspect* of quality failed?

| Domain | Code | Description | Scope |
|--------|------|-------------|-------|
| **Structural** | `STR` | Form, format, organization, syntactic integrity | Is the artifact well-formed? |
| **Semantic** | `SEM` | Meaning, correctness, truth, logical consistency | Is the artifact true and coherent? |
| **Pragmatic** | `PRA` | Context, fit, appropriateness, effectiveness | Does the artifact serve its purpose? |
| **Epistemic** | `EPI` | Confidence, evidence, uncertainty, verifiability | Are claims warranted by evidence? |

**Linguistic grounding.** These four domains map to the classical layers of language analysis: syntax (structural well-formedness), semantics (truth conditions and meaning), pragmatics (contextual appropriateness and communicative effectiveness), and epistemics (evidential basis and warrant for claims). This hierarchy is exhaustive — any quality judgment about an artifact can be located within one of these four layers.

**Orthogonality.** The domains are designed to be non-overlapping at the primary classification level. A finding that appears to span domains receives a single primary classification (the most actionable domain) with optional secondary codes. See §6.4 for multi-classification guidelines.

### 4.2 Level 2: Failure Modes

Specific failure patterns within each domain. All mode codes are **fully-qualified with domain prefix** to avoid collision (e.g., `STR-INC` for structural inconsistency vs. `SEM-INC` for semantic incorrectness).

#### 4.2.1 Structural Failures (STR)

Structural failures concern the **form** of an artifact — whether elements are present, absent, well-formed, consistent, and syntactically valid. These failures are typically detectable without understanding the artifact's meaning.

| Mode | Code | Description | Detection Signal | Example |
|------|------|-------------|------------------|---------|
| **Omission** | `STR-OMI` | Required element missing | "Missing X", "No Y found", "Expected Z" | Missing required section in README |
| **Excess** | `STR-EXC` | Unnecessary or redundant element present | "Unused X", "Redundant Y", "Dead code" | Unused import, redundant API endpoint |
| **Malformation** | `STR-MAL` | Element present but wrong structure | "Invalid format", "Malformed X" | JSON with missing closing bracket |
| **Inconsistency** | `STR-INC` | Elements contradict structurally | "X doesn't match Y", "Inconsistent naming" | camelCase mixed with snake_case |
| **Syntax** | `STR-SYN` | Syntax or formatting error in source | "Syntax error", "Parse failure" | Invalid YAML indentation, unterminated string |
| **Format** | `STR-FMT` | Layout, presentation, or formatting issue | "Wrong format", "Layout broken" | Incorrect table alignment, broken Markdown rendering |
| **Organization** | `STR-ORG` | Content present but ungrouped or poorly ordered | "Ungrouped", "Listed flat", "No structure" | Findings listed per-agent instead of pooled by theme |

> **Disambiguation: STR-MAL vs. STR-SYN.** `STR-MAL` applies when an element's *structure* is wrong (e.g., a JSON object where an array is expected). `STR-SYN` applies when the source text fails to parse at all (e.g., invalid YAML). MAL is a well-formed-but-wrong-shape problem; SYN is a not-even-parseable problem.

> **Disambiguation: STR-SYN vs. STR-FMT.** `STR-SYN` is a syntax error that prevents correct interpretation. `STR-FMT` is a presentation or layout issue that does not affect parseability but affects readability or standards compliance.

#### 4.2.2 Semantic Failures (SEM)

Semantic failures concern the **meaning** of an artifact — whether its content is correct, complete, unambiguous, logically consistent, and type-safe. These failures require understanding what the artifact means.

| Mode | Code | Description | Detection Signal | Example |
|------|------|-------------|------------------|---------|
| **Incorrectness** | `SEM-INC` | Factually or logically wrong | "Incorrect X", "Wrong Y", "Bug in Z" | Function returns wrong value |
| **Incompleteness** | `SEM-COM` | Partially correct, missing key aspects | "Incomplete handling", "Missing edge case" | Error handler catches only one of three error types |
| **Ambiguity** | `SEM-AMB` | Multiple valid interpretations | "Unclear X", "Ambiguous Y" | API parameter with no type constraint |
| **Incoherence** | `SEM-COH` | Internal logical contradiction | "X contradicts Y", "Inconsistent logic" | Documentation says "required" but schema says "optional" |
| **Type Error** | `SEM-TYP` | Type system violation | "Type mismatch", "Invalid cast" | String passed where number expected |
| **Logic Error** | `SEM-LOG` | Logical reasoning flaw | "Off-by-one", "Boundary error", "Wrong operator" | Loop termination condition inverted |
| **Misclassification** | `SEM-CAT` | Assigned to the wrong category, or distinct kinds conflated | "Wrong category", "Conflated with", "Not distinguished from" | Ceremony counted as necessary structure |

> **Note on `SEM-COM`.** The code `COM` denotes a *completeness failure* (i.e., incompleteness). The mnemonic is "COMpleteness is missing."

> **Disambiguation: SEM-INC vs. SEM-COM.** `SEM-INC` means the content is *wrong* — it would be correct if replaced. `SEM-COM` means the content is *partially right* but missing parts — it would be correct if supplemented.

> **Disambiguation: SEM-TYP vs. SEM-LOG.** `SEM-TYP` is about the *kind* of value (wrong type in a position). `SEM-LOG` is about the *reasoning* that produced the value (correct type, wrong logic). A function returning `"42"` instead of `42` is TYP. A function returning `41` instead of `42` due to an off-by-one error is LOG.

#### 4.2.3 Pragmatic Failures (PRA)

Pragmatic failures concern the **contextual fit** of an artifact — whether it serves its intended purpose, matches its audience, operates efficiently, and remains resilient under change. The artifact may be structurally and semantically valid but pragmatically wrong.

| Mode | Code | Description | Detection Signal | Example |
|------|------|-------------|------------------|---------|
| **Misalignment** | `PRA-ALI` | Does not serve stated purpose | "Doesn't achieve goal", "Wrong approach" | Auth system built but doesn't support SSO requirement |
| **Mismatch** | `PRA-MAT` | Wrong for audience or context | "Too complex for audience", "Wrong abstraction" | Enterprise-grade auth for a hobby project |
| **Inefficiency** | `PRA-EFF` | Achieves goal suboptimally | "Could be simpler", "Unnecessary complexity" | N+1 query pattern for data that could be batch-fetched |
| **Fragility** | `PRA-FRA` | Works now but breaks under change | "Brittle", "Tightly coupled", "Hard to maintain" | Hardcoded configuration values |
| **Documentation** | `PRA-DOC` | Missing or inadequate documentation | "Undocumented", "Stale docs", "No API reference" | Public API with no usage examples |
| **Testing** | `PRA-TST` | Insufficient test coverage | "Untested", "No regression tests", "Missing edge case tests" | Critical business logic with zero test coverage |
| **Inactionable** | `PRA-ACT` | States a problem with no actionable consequence | "So what?", "No next step", "No operational consequence" | A finding that names a risk but implies no change |

> **Disambiguation: PRA-ALI vs. PRA-MAT.** `PRA-ALI` (Misalignment): solution doesn't meet stated goal — *wrong outcome*. `PRA-MAT` (Mismatch): solution could work but wrong for context/audience — *wrong fit*.

> **Disambiguation: PRA-EFF vs. PRA-FRA.** `PRA-EFF` is suboptimal *now* — the artifact works but wastes resources. `PRA-FRA` will break *later* — the artifact works today but is brittle under change.

> **Note on PRA-DOC and PRA-TST.** These modes classify failures where an artifact lacks supporting infrastructure (documentation or tests) that pragmatically affects its fitness for purpose. The artifact itself may be structurally and semantically correct, but its practical value is diminished by the absence of these supporting artifacts.

#### 4.2.4 Epistemic Failures (EPI)

Epistemic failures concern the **evidential basis** of claims within an artifact — whether assertions are proportionate to available evidence, traceable to sources, and amenable to verification or falsification. The epistemic domain classifies *judgment failures* — gaps between what is claimed and what is warranted.

| Mode | Code | Description | Detection Signal | Example |
|------|------|-------------|------------------|---------|
| **Overclaiming** | `EPI-OVR` | Confidence exceeds evidence | "Unsubstantiated claim", "No evidence for X" | "Blazing fast" without benchmarks |
| **Underclaiming** | `EPI-UND` | Evidence exceeds expressed confidence | "Buried important finding", "Underplayed risk" | Security vulnerability mentioned in passing |
| **Ungrounded** | `EPI-GRN` | Claims without traceable support | "Source missing", "Unverified assumption" | Claim references unnamed "industry standard" |
| **Unfalsifiable** | `EPI-FAL` | No way to verify or refute | "Vague claim", "Unmeasurable assertion" | "The system is user-friendly" with no metric |
| **Validation Gap** | `EPI-VAL` | Validation or verification gap | "No acceptance criteria", "Untested assumption" | Requirement with no corresponding validation step |
| **Unverifiable** | `EPI-VER` | Claim cannot be independently verified | "No reproduction steps", "Cannot confirm" | Performance claim with no reproducible benchmark |
| **Scope** | `EPI-SCP` | Examined scope or evidence gaps left undeclared | "Scope not stated", "Limits not noted", "Gaps not flagged" | An audit that does not say what it did not examine |

> **Note on Reproducibility.** Issues like "cannot reproduce locally", "flaky test", or "no steps to verify" map to `EPI-VER` (unverifiable) when the claim itself cannot be independently confirmed, or `EPI-GRN` (ungrounded) when the claim lacks traceable source support entirely.

> **Note on Underclaiming (EPI-UND).** Default severity should rarely exceed `L` (low) unless underclaiming materially obscures a critical risk — e.g., downplaying a security vulnerability or burying a breaking change in release notes.

> **Disambiguation: EPI-GRN vs. EPI-VER.** `EPI-GRN` means the claim has *no traceable support* — you can't find where it came from. `EPI-VER` means the claim has a source but *cannot be independently confirmed* — you know the claim but can't check it yourself.

> **Disambiguation: EPI-OVR vs. EPI-FAL.** `EPI-OVR` means the claim *exceeds* available evidence (overclaiming). `EPI-FAL` means the claim is *structurally untestable* — no evidence could confirm or refute it.

> **Disambiguation: EPI-VAL vs. EPI-VER.** `EPI-VAL` is a *process gap* — there is no validation step defined for a claim. `EPI-VER` is a *verification capability gap* — a claim exists but there is no way to independently check it.

### 4.3 Level 3: Severity

Impact classification aligned with the UluOps canonical 5-level severity scale.

| Severity | Code | Agent Response | Threshold Impact |
|----------|------|----------------|------------------|
| **Critical** | `C` | Auto-fail trigger | Blocks progression regardless of score |
| **High** | `H` | Heavy point deduction (5–10 pts) | Significantly impacts score |
| **Medium** | `M` | Moderate deduction (3–5 pts) | Notable score impact |
| **Low** | `L` | Light deduction (1–2 pts) | Modest score impact |
| **Info** | `I` | Note only, no points | Informational, no gate impact |

#### Severity vs. Priority

These are distinct concepts tracked separately:

| Concept | Definition | Values | Example |
|---------|------------|--------|---------|
| **Severity** | Impact/risk if unfixed (objective) | critical / high / medium / low / info | Security vulnerability = critical severity |
| **Priority** | Scheduling/triage urgency (contextual) | critical / suggested / backlog | Critical severity + low likelihood = suggested priority |

### 4.4 Compound Classification Code

Full classification uses the format: `{DOMAIN}-{MODE}/{SEVERITY}`

This compound code encodes domain, mode, and severity in a single parseable token. Examples:

- `STR-OMI/C` — Critical structural omission (e.g., missing security headers)
- `SEM-INC/H` — High-severity semantic incorrectness (e.g., wrong algorithm)
- `SEM-TYP/M` — Medium-severity type error (e.g., string where number expected)
- `PRA-FRA/M` — Medium-severity pragmatic fragility (e.g., hardcoded config)
- `PRA-DOC/L` — Low-severity documentation failure (e.g., missing inline comments)
- `EPI-OVR/I` — Informational epistemic overclaim (e.g., "blazing fast" without benchmarks)
- `EPI-VER/M` — Medium-severity unverifiable claim (e.g., performance claim with no benchmark)

---

## 5. Canonical Representation

### 5.1 Codes vs. Words

The taxonomy uses **two representations** with clear roles:

| Representation | Format | Used For |
|----------------|--------|----------|
| **Word labels** | `structural`, `semantic`, `pragmatic`, `epistemic` | Human authoring (YAML), UI display |
| **Codes** | `STR`, `SEM`, `PRA`, `EPI` | Storage, queries, analysis |

**Rule**: Codes are canonical for storage and interchange. Word labels are derived for readability.

### 5.2 Derivation Rules

```
# Domain word → code mapping
structural  → STR
semantic    → SEM
pragmatic   → PRA
epistemic   → EPI

# Mode code → domain derivation (automatic)
STR-*  → STR
SEM-*  → SEM
PRA-*  → PRA
EPI-*  → EPI
```

**Validation constraint**: If both `failure_domain` and `failure_mode` are present, the mode's prefix MUST match the domain code.

**Storage rule**: `failure_domain` is derived from `failure_mode` and stored redundantly for indexing performance. When `failure_mode` is present, `failure_domain` MUST be derived from it (not independently specified).

### 5.2a Mode Boundaries

Modes that sit close together are distinguished here so the distinction survives
classification time rather than being re-litigated per finding.

| pair | boundary |
|---|---|
| `STR-ORG` vs `SEM-CAT` | `STR-ORG` is how output is **grouped**; `SEM-CAT` is whether a label is **right**. *"Gaps organised by mechanism type"* is `STR-ORG`; *"posture classified with evidence"* is `SEM-CAT`. |
| `STR-ORG` vs `STR-FMT` | `STR-FMT` is how it **looks**; `STR-ORG` is how it is **arranged**. |
| `PRA-ACT` vs `PRA-ALI` | Different subject. `PRA-ALI` is the **artifact** failing its purpose; `PRA-ACT` is the **finding** giving its reader nothing to act on. A correct, well-aimed finding can still be inactionable. |
| `EPI-SCP` vs `EPI-OVR` | `EPI-OVR` is going **beyond** what you know; `EPI-SCP` is **not saying where the edge was**. A perfectly calibrated agent can still fail `EPI-SCP`. |
| `SEM-CAT` vs `SEM-INC` | A wrong category is a wrong **classification**, not a wrong **claim**. |

**Out-of-charter content in an agent's own output is `STR-EXC`** (*"Unnecessary element
present"*). An analyst that adds prescriptions, a present-tense agent that projects, a lens
that drifts into another lens's method — none of these has failed its purpose, so `PRA-ALI` is
the wrong reading. They did their job **and something extra**, which is excess.

This is stated because the corpus has been reaching for a code to express it under at least
six different spellings across four domains. The answer is an existing mode; it simply was
never named.

### 5.3 Severity Consistency Rule

**Hard rule**: When both `severity` (word) and `failure_code` (with embedded severity) are present, they MUST match:

| Word | Code |
|------|------|
| `critical` | `C` |
| `high` | `H` |
| `medium` | `M` |
| `low` | `L` |
| `info` | `I` |

Implementations MUST reject or normalize mismatches. This eliminates downstream ambiguity.

---

## 6. Classification Guidelines

### 6.1 Domain Selection Decision Tree

```
Is the problem about form/format/structure?
├── Yes → STRUCTURAL (STR)
│   ├── Is something missing? → STR-OMI (Omission)
│   ├── Is something extra? → STR-EXC (Excess)
│   ├── Is something malformed? → STR-MAL (Malformation)
│   ├── Do parts conflict structurally? → STR-INC (Inconsistency)
│   ├── Does the source fail to parse? → STR-SYN (Syntax)
│   └── Is the layout/presentation wrong? → STR-FMT (Format)
│
├── Is the problem about correctness/truth/logic?
│   Yes → SEMANTIC (SEM)
│   ├── Is it factually wrong? → SEM-INC (Incorrectness)
│   ├── Is it partially correct? → SEM-COM (Incompleteness)
│   ├── Is it unclear what's meant? → SEM-AMB (Ambiguity)
│   ├── Does it contradict itself logically? → SEM-COH (Incoherence)
│   ├── Is the wrong type used? → SEM-TYP (Type Error)
│   └── Is there a reasoning flaw? → SEM-LOG (Logic Error)
│
├── Is the problem about fit/context/effectiveness?
│   Yes → PRAGMATIC (PRA)
│   ├── Wrong approach for the goal? → PRA-ALI (Misalignment)
│   ├── Wrong for the audience? → PRA-MAT (Mismatch)
│   ├── Works but poorly? → PRA-EFF (Inefficiency)
│   ├── Works now but fragile? → PRA-FRA (Fragility)
│   ├── Missing or stale documentation? → PRA-DOC (Documentation)
│   └── Missing or inadequate tests? → PRA-TST (Testing)
│
└── Is the problem about claims/confidence/evidence?
    Yes → EPISTEMIC (EPI)
    ├── Claims more than shown? → EPI-OVR (Overclaiming)
    ├── Shows more than claims? → EPI-UND (Underclaiming)
    ├── No source for claim? → EPI-GRN (Ungrounded)
    ├── Claim can't be tested? → EPI-FAL (Unfalsifiable)
    ├── No validation step defined? → EPI-VAL (Validation Gap)
    └── Claim can't be independently checked? → EPI-VER (Unverifiable)
```

### 6.2 Mode Disambiguation

#### Quick Reference

| Confusion Pair | Disambiguation Rule |
|----------------|---------------------|
| `STR-MAL` vs `STR-SYN` | MAL = wrong structure, parseable; SYN = fails to parse |
| `STR-SYN` vs `STR-FMT` | SYN = prevents interpretation; FMT = presentation only |
| `SEM-INC` vs `SEM-COM` | INC = completely wrong; COM = partially right, missing parts |
| `SEM-AMB` vs `SEM-COH` | AMB = unclear meaning; COH = clear but contradictory |
| `SEM-TYP` vs `SEM-LOG` | TYP = wrong kind of value; LOG = right kind, wrong logic |
| `PRA-ALI` vs `PRA-MAT` | ALI = wrong outcome; MAT = wrong fit for context |
| `PRA-EFF` vs `PRA-FRA` | EFF = suboptimal now; FRA = will break later |
| `PRA-DOC` vs `PRA-TST` | DOC = missing written guidance; TST = missing automated verification |
| `EPI-OVR` vs `EPI-FAL` | OVR = claim exceeds evidence; FAL = claim can't be tested |
| `EPI-GRN` vs `EPI-VER` | GRN = no traceable source; VER = source exists but can't be checked |
| `EPI-VAL` vs `EPI-VER` | VAL = no validation step defined; VER = no way to independently check |

### 6.3 Severity Selection Criteria

| Severity | Code | Criteria |
|----------|------|----------|
| **Critical** | `C` | Security risk, data loss risk, blocks core functionality, auto-fail condition |
| **High** | `H` | Significant quality degradation, user-facing impact, violates best practice |
| **Medium** | `M` | Moderate quality impact, affects maintainability, deviation from standards |
| **Low** | `L` | Suboptimal but functional, technical debt, style issue with minor impact |
| **Info** | `I` | Suggestion for improvement, no immediate impact, "nice to have" |

### 6.4 Multi-Classification

Some failures span domains. Use **one primary classification** for comparability, with optional secondary codes (maximum 3):

| Scenario | Primary | Secondary | Rationale |
|----------|---------|-----------|-----------|
| Wrong AND unclear | `SEM-INC` | `SEM-AMB` | Incorrectness is more actionable |
| Missing AND fragile | `STR-OMI` | `PRA-FRA` | Structural fix addresses root cause |
| Overclaim AND unmeasurable | `EPI-OVR` | `EPI-FAL` | Overclaiming is the visible symptom |
| Logic error AND untested | `SEM-LOG` | `PRA-TST` | The bug is the primary finding; missing tests are secondary |
| Type error AND undocumented | `SEM-TYP` | `PRA-DOC` | The error is primary; documentation gap is secondary |

**Selection heuristic**: When in doubt, classify by *root cause* (most actionable domain) as primary.

---

## 7. ADL Schema Integration

The UluOps Agent Definition Language (ADL) schema includes native support for failure taxonomy at multiple levels.

### 7.1 Schema Definitions

```yaml
# $defs added to adl-schema.json

# Word labels for human authoring
failureDomainLabel:
  type: string
  enum: [structural, semantic, pragmatic, epistemic]

# Codes for storage/queries
failureDomainCode:
  type: string
  enum: [STR, SEM, PRA, EPI]

# Fully-qualified mode codes (PATTERN-BASED for forward compatibility)
failureModeCode:
  type: string
  pattern: "^(STR|SEM|PRA|EPI)-[A-Z]{3}$"
  $comment: >
    Known codes v1.0.0:
    STR-OMI, STR-EXC, STR-MAL, STR-INC, STR-SYN, STR-FMT,
    SEM-INC, SEM-COM, SEM-AMB, SEM-COH, SEM-TYP, SEM-LOG,
    PRA-ALI, PRA-MAT, PRA-EFF, PRA-FRA, PRA-DOC, PRA-TST,
    EPI-OVR, EPI-UND, EPI-GRN, EPI-FAL, EPI-VAL, EPI-VER

# Severity codes
severityCode:
  type: string
  enum: [C, H, M, L, I]

# Complete failure code pattern (PATTERN-BASED for forward compatibility)
failureCodePattern:
  type: string
  pattern: "^(STR|SEM|PRA|EPI)-[A-Z]{3}/[CHMLI]$"

# Criterion-level taxonomy (definition time)
failureTaxonomy:
  type: object
  properties:
    domain:
      $ref: "#/$defs/failureDomainLabel"
      description: "Word label for human readability (normalized to code on ingest)"
    failure_mode:
      $ref: "#/$defs/failureModeCode"
    default_severity:
      enum: [critical, high, medium, low, info]
  required: [failure_mode]

# Classification provenance tracking
classificationMetadata:
  type: object
  properties:
    classified_by:
      enum: [agent, classifier, human]
      description: "Source of classification"
    confidence:
      enum: [high, medium, low]
      description: "Classifier confidence level"
    taxonomy_version:
      type: string
      pattern: "^\\d+\\.\\d+\\.\\d+$"
      description: "Version of taxonomy used for classification"
  required: [classified_by, taxonomy_version]

# Issue-level classification (runtime)
failureClassification:
  type: object
  properties:
    failure_domain:
      $ref: "#/$defs/failureDomainCode"
      description: "Derived from failure_mode, stored for indexing"
    failure_mode:
      $ref: "#/$defs/failureModeCode"
    failure_code:
      $ref: "#/$defs/failureCodePattern"
    secondary_failure_codes:
      type: array
      items:
        $ref: "#/$defs/failureCodePattern"
      maxItems: 3
    classification_metadata:
      $ref: "#/$defs/classificationMetadata"
  required: [failure_mode]
```

### 7.2 Classification Strategies

#### Strategy A: Criterion-Level Taxonomy (Static)

Define expected failure types at agent definition time. Best for well-understood failure patterns.

```yaml
agent:
  scoring:
    categories:
      - id: error_handling
        name: "Error Handling"
        points: 25
        criteria:
          - id: no_silent_failures
            name: "No silent failure swallowing"
            points: 8
            failure_taxonomy:
              domain: semantic
              failure_mode: SEM-COM
              default_severity: high
            verification:
              method: automated
              automation:
                tool: grep
                pattern: "catch.*\\{\\s*\\}"
                threshold: 0
```

#### Strategy B: Issue-Level Classification (Dynamic)

Classify at runtime when issues are detected. Best for agents that catch varied failure types.

```yaml
agent:
  output:
    classification:
      enabled: true
      require_classification: false  # Optional for backward compatibility
      allow_secondary: false         # Enable for cross-domain issues
      default_taxonomy_version: "1.0.0"
```

#### Strategy C: Hybrid (Recommended)

Define defaults at criterion level, allow runtime override at issue level.

```yaml
agent:
  scoring:
    categories:
      - id: security
        criteria:
          - id: no_hardcoded_secrets
            points: 10
            failure_taxonomy:
              domain: semantic
              failure_mode: SEM-INC
              default_severity: critical
  output:
    classification:
      enabled: true
      allow_secondary: true
      default_taxonomy_version: "1.0.0"
```

**Runtime Issue Output:**
```json
{
  "criterion_id": "no_hardcoded_secrets",
  "severity": "critical",
  "priority": "critical",
  "failure_classification": {
    "failure_domain": "SEM",
    "failure_mode": "SEM-INC",
    "failure_code": "SEM-INC/C",
    "secondary_failure_codes": ["PRA-FRA/H"],
    "classification_metadata": {
      "classified_by": "agent",
      "confidence": "high",
      "taxonomy_version": "1.0.0"
    }
  },
  "location": {
    "file": "src/config.ts",
    "line": 42
  },
  "description": "Hardcoded API key detected",
  "recommendation": "Move to environment variable"
}
```

### 7.3 Forward Compatibility

**Critical design decision**: `failureModeCode` and `failureCodePattern` use **pattern validation** rather than strict enums. This means:

1. Adding a new mode (e.g., `SEM-NEW`) does NOT require a schema change
2. Agents using older schema versions can still process new modes
3. Unknown-but-well-formed modes pass **schema** validation, and **today they are also
   accepted and stored at ingest.** Nothing on any live write path checks membership.
   `deriveFailureTaxonomy` validates `failure_domain` against a domain enum and
   `failure_severity_code` against a severity enum, but passes `failure_mode` through raw
   in both of its return branches — that asymmetry is the structural reason 242 distinct
   invented codes reached the datastore. `issues.failure_mode` is a plain string with no
   foreign key and no CHECK against `failure_taxonomy`, and migration `040` widened it to
   admit free-text names rather than narrowing it.

   Membership enforcement is **specified but not shipped.** It goes at exactly two sites —
   an ADL schema enum for build time, and an ops-api ingest membership check for the agent
   that invents a code at runtime anyway — and it ships **last**, deliberately, because
   enforcing before the corpus is migrated would reject 93 agents and 206 declarations.

   Schema-level forward compatibility (points 1 and 2) is unaffected either way: the
   pattern admits future modes, and adding one remains a data change rather than a schema
   change.

   > **This paragraph previously asserted the opposite** — that non-members were "rejected
   > at ingest" by a catalog check in `deriveFailureTaxonomy`. No such check has ever
   > existed at any layer. The claim is recorded here rather than silently deleted because
   > a spec that describes an unshipped guard as shipped is worse than one that omits it:
   > a reader concludes ingest is defended and stops defending it.

Known modes are documented in `$comment` fields for reference, not enforced via enum.

---

## 8. Validation Tracker Integration

### 8.1 Database Schema

The UluOps Validation Tracker stores failure taxonomy data in the `issues` table and a reference table for the taxonomy itself.

#### Reference Table

```sql
CREATE TABLE failure_taxonomy (
  id INT AUTO_INCREMENT PRIMARY KEY,
  domain_code VARCHAR(3) NOT NULL,
  domain_name VARCHAR(20) NOT NULL,
  mode_code VARCHAR(3) NOT NULL,
  mode_name VARCHAR(20) NOT NULL,
  description VARCHAR(255) NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  UNIQUE KEY uk_domain_mode (domain_code, mode_code)
);
```

#### Issues Table Extensions

```sql
-- Taxonomy columns on issues table
ALTER TABLE issues ADD COLUMN failure_domain VARCHAR(3);
ALTER TABLE issues ADD COLUMN failure_mode VARCHAR(7);
ALTER TABLE issues ADD COLUMN failure_code VARCHAR(9);
ALTER TABLE issues ADD COLUMN secondary_failure_codes JSON;
ALTER TABLE issues ADD COLUMN classification_fingerprint VARCHAR(64);

-- Provenance columns
ALTER TABLE issues ADD COLUMN classified_by VARCHAR(10);
ALTER TABLE issues ADD COLUMN classification_confidence VARCHAR(6);
ALTER TABLE issues ADD COLUMN taxonomy_version VARCHAR(11);

-- Indexes
CREATE INDEX idx_issues_failure_domain ON issues(failure_domain);
CREATE INDEX idx_issues_failure_mode ON issues(failure_mode);
CREATE INDEX idx_issues_failure_code ON issues(failure_code);
CREATE INDEX idx_issues_taxonomy_status ON issues(failure_domain, failure_mode, status);
CREATE INDEX idx_issues_classified_by ON issues(classified_by);
CREATE INDEX idx_issues_taxonomy_version ON issues(taxonomy_version);
CREATE INDEX idx_issues_provenance_composite
  ON issues(taxonomy_version, classified_by, classification_confidence);
```

### 8.2 Validation Rules

```typescript
interface ClassificationMetadata {
  classified_by: 'agent' | 'classifier' | 'human';
  confidence: 'high' | 'medium' | 'low';
  taxonomy_version: string;
}

interface FailureClassification {
  failure_domain?: 'STR' | 'SEM' | 'PRA' | 'EPI';
  failure_mode: string;
  failure_code?: string;
  secondary_failure_codes?: string[];
  classification_metadata?: ClassificationMetadata;
}

function validateClassification(issue: Issue): ValidationResult {
  const errors: string[] = [];

  // Rule 1: Mode prefix must match domain (if both present)
  if (issue.failure_domain && issue.failure_mode) {
    const modePrefix = issue.failure_mode.split('-')[0];
    if (modePrefix !== issue.failure_domain) {
      errors.push(`Mode prefix ${modePrefix} doesn't match domain ${issue.failure_domain}`);
    }
  }

  // Rule 2: Derive failure_domain from failure_mode
  if (issue.failure_mode && !issue.failure_domain) {
    issue.failure_domain = issue.failure_mode.split('-')[0] as any;
  }

  // Rule 3: Severity in failure_code must match severity field
  if (issue.severity && issue.failure_code) {
    const severityMap: Record<string, string> = {
      critical: 'C', high: 'H', medium: 'M', low: 'L', info: 'I'
    };
    const codeSeverity = issue.failure_code.split('/')[1];
    if (severityMap[issue.severity] !== codeSeverity) {
      errors.push(`Severity mismatch: ${issue.severity} vs code ${codeSeverity}`);
    }
  }

  // Rule 4: failure_mode format (forward-compatible pattern)
  const modePattern = /^(STR|SEM|PRA|EPI)-[A-Z]{3}$/;
  if (issue.failure_mode && !modePattern.test(issue.failure_mode)) {
    errors.push(`Invalid failure_mode format: ${issue.failure_mode}`);
  }

  // Rule 5: failure_code format
  const codePattern = /^(STR|SEM|PRA|EPI)-[A-Z]{3}\/[CHMLI]$/;
  if (issue.failure_code && !codePattern.test(issue.failure_code)) {
    errors.push(`Invalid failure_code format: ${issue.failure_code}`);
  }

  // Rule 6: Confidence required for classifier/human sources
  if (issue.classification_metadata) {
    const { classified_by, confidence } = issue.classification_metadata;
    if ((classified_by === 'classifier' || classified_by === 'human') && !confidence) {
      errors.push(`Confidence required for ${classified_by} classification source`);
    }
  }

  return { valid: errors.length === 0, errors };
}

// Helper: Generate failure_code from components
function generateFailureCode(
  mode: string,
  severity: 'critical' | 'high' | 'medium' | 'low' | 'info'
): string {
  const severityMap: Record<string, string> = {
    critical: 'C', high: 'H', medium: 'M', low: 'L', info: 'I'
  };
  return `${mode}/${severityMap[severity]}`;
}
```

### 8.3 Query Patterns

```sql
-- Distribution by failure domain
SELECT
  failure_domain,
  COUNT(*) as count,
  ROUND(100.0 * COUNT(*) / SUM(COUNT(*)) OVER (), 1) as pct
FROM issues
WHERE failure_domain IS NOT NULL
GROUP BY failure_domain;

-- Most common failure modes by project
SELECT
  p.name as project,
  i.failure_mode,
  COUNT(*) as count
FROM issues i
JOIN projects p ON i.project_id = p.id
WHERE i.failure_mode IS NOT NULL
GROUP BY p.name, i.failure_mode
ORDER BY p.name, count DESC;

-- Agent blind spots (modes never detected by a given agent)
WITH all_modes AS (
  SELECT CONCAT(domain_code, '-', mode_code) as full_code
  FROM failure_taxonomy
),
agent_coverage AS (
  SELECT DISTINCT r.agent, i.failure_mode
  FROM issues i
  JOIN occurrences o ON i.id = o.issue_id
  JOIN runs r ON o.run_id = r.id
  WHERE i.failure_mode IS NOT NULL
)
SELECT a.agent, m.full_code as uncovered_mode
FROM (SELECT DISTINCT agent FROM runs) a
CROSS JOIN all_modes m
LEFT JOIN agent_coverage c
  ON a.agent = c.agent AND m.full_code = c.failure_mode
WHERE c.failure_mode IS NULL;

-- Classification provenance breakdown
SELECT
  classified_by,
  classification_confidence,
  taxonomy_version,
  COUNT(*) as count
FROM issues
WHERE classified_by IS NOT NULL
GROUP BY classified_by, classification_confidence, taxonomy_version;

-- Cognitive parallax: mode distribution per thinker lens
SELECT
  r.agent,
  i.failure_domain,
  i.failure_mode,
  COUNT(*) as count
FROM issues i
JOIN occurrences o ON i.id = o.issue_id
JOIN runs r ON o.run_id = r.id
WHERE i.failure_mode IS NOT NULL
GROUP BY r.agent, i.failure_domain, i.failure_mode
ORDER BY r.agent, count DESC;
```

---

## 9. Example Agent with Taxonomy

```yaml
agent:
  metadata:
    name: api-contract-validator
    version: "2.0.0"
    description: "Validates API contracts for consistency, completeness, and correctness"
    domain: software
    tools: [Read, Grep, Glob]
    model: sonnet

  scoring:
    total_points: 100
    categories:
      - id: schema_structure
        name: "Schema Structure"
        points: 25
        criteria:
          - id: valid_openapi
            name: "Valid OpenAPI 3.x syntax"
            points: 10
            failure_taxonomy:
              domain: structural
              failure_mode: STR-SYN
              default_severity: critical

          - id: required_fields
            name: "All required fields present"
            points: 8
            failure_taxonomy:
              domain: structural
              failure_mode: STR-OMI
              default_severity: high

          - id: no_redundant_endpoints
            name: "No duplicate/redundant endpoints"
            points: 7
            failure_taxonomy:
              domain: structural
              failure_mode: STR-EXC
              default_severity: medium

      - id: semantic_correctness
        name: "Semantic Correctness"
        points: 25
        criteria:
          - id: accurate_response_types
            name: "Response types match actual behavior"
            points: 8
            failure_taxonomy:
              domain: semantic
              failure_mode: SEM-TYP
              default_severity: high

          - id: complete_error_handling
            name: "All error cases documented"
            points: 8
            failure_taxonomy:
              domain: semantic
              failure_mode: SEM-COM
              default_severity: medium

          - id: no_logic_errors
            name: "Status codes logically consistent"
            points: 9
            failure_taxonomy:
              domain: semantic
              failure_mode: SEM-LOG
              default_severity: high

      - id: pragmatic_fit
        name: "Pragmatic Fit"
        points: 25
        criteria:
          - id: adequate_documentation
            name: "Endpoints have usage documentation"
            points: 10
            failure_taxonomy:
              domain: pragmatic
              failure_mode: PRA-DOC
              default_severity: medium

          - id: maintainable_structure
            name: "Contract structure supports evolution"
            points: 8
            failure_taxonomy:
              domain: pragmatic
              failure_mode: PRA-FRA
              default_severity: medium

          - id: efficient_payloads
            name: "Payload sizes are appropriate"
            points: 7
            failure_taxonomy:
              domain: pragmatic
              failure_mode: PRA-EFF
              default_severity: low

      - id: epistemic_integrity
        name: "Epistemic Integrity"
        points: 25
        criteria:
          - id: grounded_examples
            name: "Examples are testable and accurate"
            points: 8
            failure_taxonomy:
              domain: epistemic
              failure_mode: EPI-GRN
              default_severity: medium

          - id: honest_descriptions
            name: "Descriptions don't overclaim capabilities"
            points: 7
            failure_taxonomy:
              domain: epistemic
              failure_mode: EPI-OVR
              default_severity: low

          - id: verifiable_constraints
            name: "Constraints are independently verifiable"
            points: 5
            failure_taxonomy:
              domain: epistemic
              failure_mode: EPI-VER
              default_severity: low

          - id: validation_coverage
            name: "All assertions have validation steps"
            points: 5
            failure_taxonomy:
              domain: epistemic
              failure_mode: EPI-VAL
              default_severity: medium

  output:
    classification:
      enabled: true
      allow_secondary: true
      default_taxonomy_version: "1.0.0"
```

---

## 10. Novelty & Patent Claims

### 10.1 Summary of Novel Contributions

The UluOps Failure Taxonomy introduces several independently novel elements, and their combination constitutes a system not found in prior art:

1. **Linguistically-grounded failure ontology for AI validation.** The four-domain structure (Structural, Semantic, Pragmatic, Epistemic) is derived from the hierarchy of language analysis and applied to the classification of AI-detected quality failures in arbitrary artifacts. No prior system uses this specific linguistic grounding as the basis for an AI validation taxonomy.

2. **Epistemic domain as a first-class failure category.** Traditional quality and bug taxonomies (IEEE 1044, ODC, etc.) classify structural and semantic defects but do not treat *evidential status of claims* as a distinct failure domain. The epistemic domain — with modes for overclaiming, underclaiming, ungroundedness, unfalsifiability, validation gaps, and unverifiability — is novel.

3. **Collision-free compound classification codes.** The `{DOMAIN}-{MODE}/{SEVERITY}` compound code format encodes domain, mode, and severity in a single parseable token with zero ambiguity. The fully-qualified mode prefix (e.g., `STR-INC` vs. `SEM-INC`) prevents collision without requiring contextual disambiguation.

4. **Forward-compatible schema via pattern validation.** The use of regex patterns rather than enumerations for mode codes allows the taxonomy to be extended without schema changes, while preserving backward compatibility with existing agents and data.

5. **Classification provenance tracking.** Each classification records its source (agent, classifier, or human), confidence level, and taxonomy version, enabling analysis of classification reliability and systematic backfill of historical data.

6. **Cognitive parallax measurement.** The taxonomy enables quantitative measurement of finding overlap across agents operating through different cognitive lenses (e.g., different thinker profiles). The empirical finding that different lenses produce near-zero overlapping classifications on the same artifact is a discovery enabled by this taxonomy.

7. **Recursive appreciation via taxonomy.** The taxonomy can be applied reflexively — agents can classify their own blind spots using the same system, enabling the measurement of self-evaluation effectiveness. The empirical ~80% convergence ceiling (Gödelian boundary) on self-evaluation was characterized using this classification system.

8. **Domain-portable application.** The same taxonomy applies across software artifacts, documents, prompts, specifications, API contracts, and agent definitions, without modification. This portability is a consequence of the linguistic grounding — the four domains are properties of any artifact that can be subject to quality judgment.

### 10.2 Relationship to Existing Patents

This specification relates to the following UluOps provisional patents:

- **Patent A**: Fingerprint-based issue correlation — the taxonomy is excluded from fingerprint computation to prevent classifier jitter from breaking deduplication
- **Patent B**: Recursive validation architecture — the taxonomy provides the classification layer within the recursive validation pipeline

### 10.3 Prior Art Differentiation

| Prior Art | Key Difference |
|-----------|----------------|
| IEEE 1044 (Software Anomaly Classification) | No epistemic domain; not designed for AI agents; no compound codes |
| Orthogonal Defect Classification (ODC) | Process-oriented (trigger/impact); no linguistic grounding; no epistemic dimension |
| CWE / CVE | Security-specific; enumerative rather than taxonomic; no portability to non-security domains |
| OWASP classifications | Web-security-specific; structural only; no pragmatic or epistemic dimensions |
| Traditional bug tracking categories | Ad hoc, project-specific; not linguistically grounded; not machine-classifiable |

---

## 11. Resolved Design Decisions

| Decision | Resolution | Rationale |
|----------|------------|-----------|
| Mode code collision (`INC`) | Fully-qualified codes required | `STR-INC` vs `SEM-INC` are unambiguous |
| Severity scale | 5-level (critical/high/medium/low/info) | Backwards compatible, sufficient granularity |
| Severity vs Priority | Track both separately | Different concepts: impact vs scheduling |
| Multi-classification | Primary + optional secondary (max 3) | Preserves comparability, allows nuance |
| Reproducibility issues | Map to `EPI-VER` or `EPI-GRN` | VER = can't check; GRN = no source |
| Fingerprint + taxonomy | Exclude taxonomy from dedupe | Prevents classifier jitter from breaking dedupe |
| Domain naming | Use `failure_domain` (code) in storage | Avoids collision with ADL `metadata.domain` |
| Canonical representation | Codes for storage; words for authoring/UI | Queryable + readable |
| Schema field naming | `failure_taxonomy` (definition), `failure_classification` (runtime) | Clear semantic distinction |
| Severity consistency | Hard rule: word and code must match | Eliminates downstream ambiguity |
| Classification provenance | Track `classified_by`, `confidence`, `taxonomy_version` | Enables backfill and evolution |
| EPI-UND severity | Default to Low unless material risk hidden | Prevents noise from minor underclaims |
| `failure_domain` storage | Store redundantly, derive from `failure_mode` | Indexing performance vs single source of truth |
| Schema validation | Pattern-based for modes, enum for domains/severity | Forward compatibility for taxonomy extension |
| `classified_by` values | `agent` (not `validator`) | Reflects expanded agent type vocabulary |
| New structural modes | STR-SYN + STR-FMT | Distinguishes parse failures from layout issues |
| New semantic modes | SEM-TYP + SEM-LOG | Distinguishes type violations from reasoning errors |
| New pragmatic modes | PRA-DOC + PRA-TST | Infrastructure that pragmatically affects fitness |
| New epistemic modes | EPI-VAL + EPI-VER | Distinguishes process gaps from verification capability gaps |

---

## 12. Full Code Reference

### 12.1 Domain Codes

| Code | Domain | Label | Description |
|------|--------|-------|-------------|
| `STR` | Structural | `structural` | Form, format, organization, syntactic integrity |
| `SEM` | Semantic | `semantic` | Meaning, correctness, truth, logical consistency |
| `PRA` | Pragmatic | `pragmatic` | Context, fit, effectiveness, supporting infrastructure |
| `EPI` | Epistemic | `epistemic` | Confidence, evidence, verifiability, claims |

### 12.2 Mode Codes (Fully-Qualified)

| Code | Domain | Mode | Mnemonic |
|------|--------|------|----------|
| `STR-OMI` | Structural | Omission | OMItted |
| `STR-EXC` | Structural | Excess | EXCess |
| `STR-MAL` | Structural | Malformation | MALformed |
| `STR-INC` | Structural | Inconsistency | INConsistent |
| `STR-SYN` | Structural | Syntax | SYNtax |
| `STR-FMT` | Structural | Format | ForMaT |
| `SEM-INC` | Semantic | Incorrectness | INCorrect |
| `SEM-COM` | Semantic | Incompleteness | COMpleteness failure |
| `SEM-AMB` | Semantic | Ambiguity | AMBiguous |
| `SEM-COH` | Semantic | Incoherence | COHerence failure |
| `SEM-TYP` | Semantic | Type Error | TYPe error |
| `SEM-LOG` | Semantic | Logic Error | LOGic error |
| `PRA-ALI` | Pragmatic | Misalignment | misALIgned |
| `PRA-MAT` | Pragmatic | Mismatch | MATch failure |
| `PRA-EFF` | Pragmatic | Inefficiency | EFFiciency failure |
| `PRA-FRA` | Pragmatic | Fragility | FRAgile |
| `PRA-DOC` | Pragmatic | Documentation | DOCumentation |
| `PRA-TST` | Pragmatic | Testing | TeST |
| `EPI-OVR` | Epistemic | Overclaiming | OVeRclaim |
| `EPI-UND` | Epistemic | Underclaiming | UNDerclaim |
| `EPI-GRN` | Epistemic | Ungrounded | unGRouNded |
| `EPI-FAL` | Epistemic | Unfalsifiable | unFALsifiable |
| `EPI-VAL` | Epistemic | Validation Gap | VALidation |
| `EPI-VER` | Epistemic | Unverifiable | VERifiable failure |

### 12.3 Severity Codes

| Level | Code | Point Impact |
|-------|------|--------------|
| `critical` | `C` | auto-fail |
| `high` | `H` | −5 to −10 |
| `medium` | `M` | −3 to −5 |
| `low` | `L` | −1 to −2 |
| `info` | `I` | 0 |

### 12.4 Regex Patterns

```regex
# Mode code (forward-compatible)
^(STR|SEM|PRA|EPI)-[A-Z]{3}$

# Full failure code (forward-compatible)
^(STR|SEM|PRA|EPI)-[A-Z]{3}/[CHMLI]$

# Strict mode code (known modes only, v1.1.0)
# Domain-SCOPED alternation, not a cross-product. The previous form,
# ^(STR|SEM|PRA|EPI)-(OMI|...|VER)$, admitted 4 x 23 = 92 combinations for 24 valid modes
# and therefore MATCHED SEM-VAL — the exact code this taxonomy's conformance work exists to
# eliminate. A cross-product cannot express domain-mode binding. Generated from
# failure-taxonomy-schema-defs; length is not a maintenance concern because it is not
# hand-written.
^(STR-(OMI|EXC|MAL|INC|SYN|FMT|ORG)|SEM-(INC|COM|AMB|COH|TYP|LOG|CAT)|PRA-(ALI|MAT|EFF|FRA|DOC|TST|ACT)|EPI-(OVR|UND|GRN|FAL|VAL|VER|SCP))$

# Strict failure code (known modes only, v1.1.0)
^(STR-(OMI|EXC|MAL|INC|SYN|FMT|ORG)|SEM-(INC|COM|AMB|COH|TYP|LOG|CAT)|PRA-(ALI|MAT|EFF|FRA|DOC|TST|ACT)|EPI-(OVR|UND|GRN|FAL|VAL|VER|SCP))/(C|H|M|L|I)$
```

---

## 13. Changelog

### v1.1.0 (Current)

- **Expanded taxonomy** from 24 to 28 modes (4 domains × 7 modes each), adding `STR-ORG`
  (Organization), `SEM-CAT` (Misclassification), `PRA-ACT` (Inactionable) and `EPI-SCP` (Scope).
  Each was adopted on attested corpus demand, re-measured per declaration rather than per code.
- **Slot budget recorded: 7 modes per domain, 28 total, by design.** The previous 24 was 6×4
  and equally deliberate, but was never written down — which is why proposals to extend it read
  as accretion. A slot is filled when the corpus shows recurring demand, never to complete the
  shape.
- **§5.2a Mode Boundaries added** — the adjacent-mode distinctions, and `STR-EXC` named as the
  home for out-of-charter content in an agent's own output.
- **§7.3 point 3 amended, then CORRECTED (2026-08-03).** The v1.1.0 amendment claimed
  well-formed non-member modes were "rejected at ingest" by a catalog check in
  `deriveFailureTaxonomy`. **That was false — no such check has ever existed at any layer.**
  Verified against `ops-uluops-api` `src/business-objects/failure-taxonomy.ts`:
  `deriveFailureTaxonomy` validates `failure_domain` and `failure_severity_code` against
  enums and passes `failure_mode` through raw in both return branches. §7.3 now states the
  actual behaviour — non-members are accepted and stored — and marks enforcement as
  specified-but-unshipped, shipping last by design. Schema-level forward compatibility was
  never in question. This is the third instance in this arc of a document asserting what its
  implementation lacks; the failure mode is that a reader concludes a guard exists and stops
  building it.
- **Canonical source declared:** `failure-taxonomy-schema-defs-v1_1_0.json`. Every other copy
  of the mode set derives from it.

### v1.0.0

- **Rebranded** from "Cognitive Ops" to UluOps throughout
- **Expanded taxonomy** from 16 to 24 modes (4 domains × 6 modes each):
  - Added `STR-SYN` (syntax) and `STR-FMT` (format) to Structural
  - Added `SEM-TYP` (type error) and `SEM-LOG` (logic error) to Semantic
  - Added `PRA-DOC` (documentation) and `PRA-TST` (testing) to Pragmatic
  - Added `EPI-VAL` (validation gap) and `EPI-VER` (unverifiable) to Epistemic
- **Balanced domains**: all four domains now have exactly 6 modes
- **Updated `classified_by`** values from `validator` to `agent` (reflecting expanded agent types)
- **All VDL references** updated to ADL
- **Patent-ready** language added (§10: Novelty & Patent Claims)
- **Added disambiguation notes** for all new mode pairs
- **Updated regex patterns** to include all 24 modes in strict variants
- **Updated example agent** to demonstrate new modes (`STR-SYN`, `SEM-TYP`, `SEM-LOG`, `PRA-DOC`, `EPI-VER`, `EPI-VAL`)
- **Database schema** updated from SQLite to MySQL with proper column types
- **Added cognitive parallax query** to §8.3

### v0.2.2
- Forward compatibility: Changed `failureModeCode` validation from enum to pattern
- Provenance columns: Added `classified_by`, `classification_confidence`, `taxonomy_version`
- EPI domain clarity: Added "(Judgment)" label
- SEM-COM clarity: Added mnemonic note
- PRA-ALI vs PRA-MAT: Added disambiguation rubric
- Mode disambiguation table added

### v0.2.1
- Terminology: Changed "epistemic failures" to "judgment failures" in Overview
- Canonical representation: Established codes as canonical storage, words as authoring/UI
- Schema field naming: `failure_taxonomy` (definition) vs `failure_classification` (runtime)
- Classification metadata: Added provenance tracking fields

### v0.2.0
- Collision-free mode codes (fully-qualified with domain prefix)
- Severity aligned to 5-level scale
- Explicit severity vs priority distinction
- Fingerprint strategy excluding taxonomy from dedupe

### v0.1.0
- Initial taxonomy structure (4 domains × 4 modes)
- Basic schema integration

---

*UluOps Failure Taxonomy Specification v1.0.0 — Linguistically-grounded, domain-portable classification with 24 failure modes, forward-compatible schema, full provenance tracking, and patent-ready novelty documentation.*
