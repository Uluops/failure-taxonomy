**[UluOps](https://uluops.ai)** · Operating Intelligence as Infrastructure

# Failure Taxonomy

A linguistically-grounded classification system for quality failures detected by AI validation agents. Four orthogonal domains derived from the hierarchy of language analysis — Structural, Semantic, Pragmatic — extended with a novel fourth domain, Epistemic, that treats evidential status as a first-class failure category.

## The Problem

AI validation agents produce findings in their own vocabulary, using their own implicit severity scales, from their own analytical perspectives. Without a shared classification system, it is impossible to compare findings across agents, identify systematic blind spots, track quality over time, or measure whether agents operating through different cognitive lenses are catching different failures or the same ones.

## The Solution

### Four Domains, 28 Failure Modes

| Domain | Layer | Question | Modes |
|--------|-------|----------|-------|
| **Structural (STR)** | Syntax | Is the artifact well-formed? | Omission, Excess, Malformation, Inconsistency, Syntax, Format, Organization |
| **Semantic (SEM)** | Semantics | Is the artifact correct and coherent? | Incorrectness, Incompleteness, Ambiguity, Incoherence, Type Error, Logic Error, Misclassification |
| **Pragmatic (PRA)** | Pragmatics | Does the artifact serve its purpose? | Misalignment, Mismatch, Inefficiency, Fragility, Documentation, Testing, Inactionable |
| **Epistemic (EPI)** | Epistemics | Are claims warranted by evidence? | Overclaiming, Underclaiming, Ungrounded, Unfalsifiable, Validation Gap, Unverifiable, Scope |

The first three domains draw from the established syntactic/semantic/pragmatic hierarchy of language analysis (Morris 1938, Carnap 1942, Austin 1962). The Epistemic domain is an original contribution — no prior failure taxonomy treats the evidential status of claims as a classification dimension equal to structural, semantic, and pragmatic correctness.

### Compound Classification Codes

Every finding receives a collision-free compound code encoding domain, mode, and severity in a single parseable token:

```
STR-OMI/H    Structural omission, high severity
SEM-AMB/M    Semantic ambiguity, medium severity
PRA-FRA/L    Pragmatic fragility, low severity
EPI-OVR/H    Epistemic overclaiming, high severity
```

The fully-qualified prefix eliminates collision — `STR-INC` (structural inconsistency) and `SEM-INC` (semantic incorrectness) are unambiguously distinct without contextual disambiguation.

### Forward-Compatible Schema Design

Failure mode codes are validated using pattern-based matching rather than enumerated value lists. New failure modes within existing domains are automatically accepted by existing schemas without requiring schema changes, database migrations, or agent updates. The four domain codes (STR, SEM, PRA, EPI) are a deliberate closed set — the domain structure is a design commitment, not a limitation.

## Key Properties

**Classification provenance.** Every classified finding records who or what classified it, at what confidence level, and under which taxonomy version — enabling longitudinal analysis of classification reliability.

**Multi-level classification.** Failure types can be defined at agent design time (criterion-level), assigned at runtime when findings are detected (issue-level), or both (hybrid). Design-time defaults can be overridden by runtime observations.

**Multi-classification with hierarchy.** Findings that span domains receive a single primary classification for comparability and up to three secondary classifications for nuance. The primary classification captures the root cause; secondaries capture contributing factors.

**Cognitive parallax measurement.** The shared taxonomy enables quantitative assessment of finding overlap across agents operating through different cognitive lenses. Empirical data shows near-zero overlapping classifications when different lenses evaluate the same artifact — a finding only discoverable through a shared taxonomic framework.

**Recursive self-application.** The taxonomy can classify failures in the classification agents themselves. Agents can characterize their own blind spots using the same codes they apply to artifacts, enabling measurement of self-evaluation effectiveness and detection limits.

## Related

- [Unified Definition Language](https://github.com/Uluops/unified-definition-language) — The definition languages whose agent output schemas integrate this taxonomy
- [Agents & Pipelines](https://github.com/aself101/agents-and-pipelines) — Agent definitions that produce classified findings
- [Cognitive Lens Library](https://github.com/Uluops/cognitive-lens-library) — Thinker profiles whose cognitive parallax the taxonomy measures
- [UluOps](https://uluops.ai) — The platform infrastructure for tracking and analyzing classified findings
- [UluOps Registry — Specs](https://registry.uluops.ai/specs/adl) — Live rendered specifications

## License

This work is licensed under [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/). Copyright 2025-2026 Ulu Labs Inc.
