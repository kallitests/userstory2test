# 🩺 UserStory2Test — HealthTech Prompt

> **One prompt, four regulated-ready artifacts.**
> From a raw user story to a structured Jira ticket, IEC 62304 risk classification, Gherkin scenarios, and Playwright/TypeScript step definitions — in a single JSON output.

[![Status](https://img.shields.io/badge/status-WIP-orange?style=flat-square)](https://github.com/kallitests)
[![Domain](https://img.shields.io/badge/domain-HealthTech%20%2F%20MedTech-0a7f5b?style=flat-square)](https://github.com/kallitests)
[![Playwright](https://img.shields.io/badge/Playwright-bdd-green?style=flat-square&logo=playwright)](https://playwright.dev)
[![Claude](https://img.shields.io/badge/Claude-Anthropic-black?style=flat-square)](https://anthropic.com)
[![Standards](https://img.shields.io/badge/standards-IEC%2062304%20%C2%B7%20ISO%2014971%20%C2%B7%2021%20CFR%20Part%2011-blue?style=flat-square)](https://github.com/kallitests)
[![Tokens](https://img.shields.io/badge/prompt-token--optimized%20(~45%25)-yellow?style=flat-square)](https://github.com/kallitests)

---

## 🗺️ Table of Contents

- [Why this prompt?](#-why-this-prompt)
- [What it does](#%EF%B8%8F-what-it-does)
- [Architecture](#-architecture)
- [Regulatory standards in scope](#-regulatory-standards-in-scope)
- [System prompt](#-system-prompt)
- [Token efficiency](#-token-efficiency)
- [Output schema](#-output-schema)
- [Prompt design notes](#-prompt-design-notes)
- [Example run](#%EF%B8%8F-example-run)
- [Roadmap](#-roadmap)
- [Author](#-author)

---

## 💡 Why this prompt?

In regulated medical software, a user story is never "just" a feature request — it carries
patient-risk classification, traceability obligations, and audit requirements that most teams
bolt on **after** the ticket is written.

> *"Is this story safe to ship without a documented risk justification and an audit trail?"*

This prompt answers that question **at ticket creation time**, not during the compliance
review three sprints later.

```
User Story ──▶ Jira Ticket + IEC 62304 Class ──▶ Acceptance Criteria + Test Cases ──▶ Gherkin ──▶ Playwright/TS
```

---

## ⚙️ What It Does

| Step | Description |
|------|-------------|
| 📋 **Story parsing** | Reads a raw user story and drafts a production-ready Jira ticket |
| 🩻 **Risk classification** | Assigns an IEC 62304 software safety class (A/B/C) with a one-sentence justification |
| 🔐 **PHI detection** | Flags personal health data and the applicable data protection regime (GDPR/HDS, HIPAA) |
| ✅ **Acceptance criteria** | Generates ≥5 Given/When/Then criteria, including audit-trail coverage for Class B/C or PHI stories |
| 🧪 **Test case design** | Generates ≥3 test cases (happy path, edge case, error case), plus security/data-integrity for higher-risk stories |
| 🥒 **Gherkin generation** | Converts every test case into one tagged `.feature` scenario (English) |
| 🎭 **Playwright mapping** | Generates matching `.steps.ts` step definitions (playwright-bdd) — no step left unmapped |
| 📑 **Regulatory DoD** | Adds the documentation an auditor would expect (risk analysis, traceability matrix, validation evidence) |

---

## 🏗️ Architecture

```
┌───────────────────────────────────────────────────────────────────────────┐
│                          UserStory2Test Prompt                            │
│                                                                           │
│   ┌──────────────┐     ┌────────────────┐     ┌─────────────────────┐  │
│   │  User Story  │────▶│  Jira Ticket   │────▶│ IEC 62304 Class A/B/C│  │
│   │  (input)     │     │  + AC + Tests  │     │ + PHI / GDPR-HDS flag│  │
│   └──────────────┘     └────────────────┘     └──────────┬───────────┘  │
│                                                            │              │
│   ┌──────────────┐     ┌────────────────┐     ┌──────────▼───────────┐  │
│   │ Playwright   │◀────│  Gherkin .feat │◀────│   Test Cases          │  │
│   │ .steps.ts    │     │  (tagged @TC)  │     │ (happy/edge/error/sec)│  │
│   └──────────────┘     └────────────────┘     └───────────────────────┘  │
│                                                                           │
│   📤 Single JSON output — no prose, no markdown fences                  │
└───────────────────────────────────────────────────────────────────────────┘
```

---

## 📜 Regulatory Standards in Scope

| Standard | Covers |
|----------|--------|
| **ISO 13485** | Quality management systems for medical devices |
| **IEC 62304** | Software life cycle processes — safety classification (Class A/B/C) |
| **ISO 14971** | Risk management for medical devices |
| **IEC 62366-1** | Usability engineering for medical devices |
| **ISO 15189** | Quality and competence of medical laboratories |
| **GAMP 5** | Risk-based computerized system validation (CSV) |
| **21 CFR Part 11** | Electronic records, electronic signatures, audit trails (FDA) |
| **GDPR / HDS** | Personal health data protection and hosting (EU / France) |
| **HIPAA** | Personal health data protection (US) |

> ⚠️ This prompt is a **drafting aid**, not a compliance certification. Classification,
> risk justification, and generated documentation must be reviewed and signed off by a
> qualified Quality/Regulatory Affairs role before use as regulatory evidence.

---

## 🤖 System Prompt

> Optimized for minimum input-token cost: standards referenced by code only, dense
> single-line rules, compact schema. Same output contract as before — see
> [Token Efficiency](#-token-efficiency) for the full breakdown.

```
Role: Agile BA + QA Engineer + SDET, specialized in regulated MedTech/HealthTech/Pharma software.
Task: turn one raw user story into one JSON object containing a Jira ticket, an IEC 62304 risk
classification, Gherkin scenarios, and Playwright/TypeScript step definitions.

Standards to apply (codes only — apply rules below, do not explain the standards):
ISO 13485, IEC 62304 (Class A/B/C), ISO 14971, IEC 62366-1, ISO 15189, GAMP 5,
21 CFR Part 11, GDPR/HDS, HIPAA.

Rules:
1. Jira fields: language = input story's language. Gherkin + TS code: always English.
2. Priority: infer from business impact.
3. iec62304_class: A/B/C + 1-sentence justification (potential patient harm if feature fails).
4. involves_phi: true/false; if true, list applicable_data_protection (GDPR/HDS/HIPAA).
5. acceptance_criteria: ≥5, Given/When/Then. If class B/C or PHI: ≥1 AC must cover audit
   trail (who/what/when, 21 CFR Part 11).
6. test_cases: ≥3 (happy_path, edge_case, error_case). If class B/C or PHI: add "security";
   add "data_integrity" where relevant.
7. Each test case → exactly one tagged Gherkin scenario (@TC-01...) in gherkin_feature.
   Feature file = title + As a/I want/So that + one Scenario per test case.
8. Every Gherkin step → one matching step def in playwright_steps. No step unmapped.
   Use playwright-bdd (createBdd), Playwright locators (getByRole/getByTestId), generic
   but domain-consistent selectors/routes.
9. definition_of_done: standard engineering items + regulatory artifacts matching the
   classification (ISO 14971 risk analysis update, traceability matrix entry, GAMP 5
   validation evidence).
10. labels: domain-relevant + iec62304 class (e.g. "iec62304-class-b") + "phi" if applicable.
11. story_points: Fibonacci (1,2,3,5,8,13).
12. Output ONLY the JSON object below. No prose, no markdown fences, no explanation.

Schema:
{
  "summary": str(≤10 words), "issue_type": "Story",
  "priority": "Highest|High|Medium|Low", "story_points": int, "labels": [str],
  "safety_classification": {
    "iec62304_class": "A|B|C", "justification": str,
    "involves_phi": bool, "applicable_data_protection": [str]
  },
  "description": {
    "user_story": str, "context": str, "assumptions": [str], "out_of_scope": [str]
  },
  "acceptance_criteria": [{"id":"AC-01","given":str,"when":str,"then":str}],
  "test_cases": [{
    "id":"TC-01","title":str,
    "type":"happy_path|edge_case|error_case|security|performance|data_integrity",
    "preconditions":[str],"steps":[str],"expected_result":str,"linked_ac":["AC-01"]
  }],
  "definition_of_done": [str], "technical_notes": [str],
  "gherkin_feature": str,
  "playwright_steps": str
}
```

---

## 🪙 Token Efficiency

| Change | Token saving | Why it's safe |
|---|---|---|
| Standards listed as codes only, no inline definitions | ~120 tokens | The model already knows what ISO 13485/IEC 62304/etc. mean; spelling them out was for human readers, not needed for the model to follow the rules. |
| Numbered, single-line rules instead of bulleted prose | ~150 tokens | Same instructions, denser phrasing; numbering also makes the rule set easier to reference/debug. |
| Schema without per-field comments | ~180 tokens | Field semantics are already fully specified in the rules above — repeating them in the schema was pure duplication. |
| Inline enums (`"A\|B\|C"`) instead of spelled-out options | ~20 tokens | Same constraint, shorter syntax — still prevents hallucinated enum values. |
| Dropped restated examples (e.g. label format shown once, not repeated) | ~15 tokens | One example fixes the format; repeating it added no new constraint. |

**Net effect:** ~870 → ~480 tokens (≈45% reduction) for the system prompt, with an
identical behavioral contract — same required fields, same conditional rules (Class B/C
or PHI → extra AC + extra test cases), same Gherkin/Playwright coverage guarantee.

**Output-side savings (not enabled by default):** if you also need to cut response tokens,
ask for a `compact` mode in a follow-up turn rather than baking it into the prompt, since it
trades away information still useful for audits — e.g. drop `technical_notes`, shorten
`test_cases[].steps` to phrases, or request `gherkin_feature`/`playwright_steps` only for a
preview scenario before generating the full file. These are intentionally excluded from the
default prompt above because they reduce regulatory completeness.

---

## 📐 Output Schema

The schema is defined once, inline in the [System Prompt](#-system-prompt) above (compact
notation: `str`, `int`, `bool`, `[str]`, inline enums). It is intentionally **not duplicated**
here — every field's meaning is already covered by the numbered rules, and repeating the
schema with descriptive comments was one of the redundancies removed during token
optimization (see [Token Efficiency](#-token-efficiency)).

Quick field reference:

| Field | Type | Notes |
|---|---|---|
| `summary`, `issue_type`, `priority`, `story_points`, `labels` | str / str / enum / int / [str] | Standard Jira fields |
| `safety_classification` | object | IEC 62304 class + justification + PHI flag + data protection regime |
| `description` | object | User story narrative, context, assumptions, out of scope |
| `acceptance_criteria` | [object] | ≥5, Given/When/Then |
| `test_cases` | [object] | ≥3, typed (happy_path/edge_case/error_case/security/performance/data_integrity) |
| `definition_of_done`, `technical_notes` | [str] | Includes regulatory artifacts for Class B/C or PHI |
| `gherkin_feature` | str | Full `.feature` file content, English, one scenario per test case |
| `playwright_steps` | str | Full `.steps.ts` content (playwright-bdd), 100% step coverage |

---

## 🧭 Prompt Design Notes

| Principle | Implementation |
|-----------|---------------|
| Role anchoring | Quadruple expertise (BA + QA + SDET + regulated MedTech) to keep both delivery speed and compliance posture in the same pass |
| Language detection | Jira fields follow the input language; Gherkin and code are always in English, the de facto standard for automation codebases |
| Risk-based classification | IEC 62304 software class (A/B/C) is inferred and justified per story, so QA effort and documentation scale with actual patient risk |
| Data protection awareness | Explicit PHI detection flags GDPR/HDS/HIPAA obligations before the ticket reaches engineering |
| Traceability & audit trail | Class B/C or PHI stories must include an audit-trail acceptance criterion, aligned with 21 CFR Part 11 |
| Expanded test coverage | Security and data-integrity test cases are added automatically for higher-risk stories |
| Regulatory Definition of Done | DoD items reference real audit artifacts (risk analysis update, traceability matrix, validation evidence) |
| Output constraint | "Output ONLY valid JSON" prevents prose wrapping that breaks parsing |
| Enum safety | Priority, safety class, and test case type values are listed explicitly to avoid hallucinated values |
| Fibonacci constraint | Forces realistic story point estimation |
| Traceability (test automation) | Each test case maps 1:1 to a tagged Gherkin scenario (@TC-01…), keeping Jira, Gherkin, and automated tests in sync |
| Full step coverage | Explicit rule against unmapped steps prevents a `.feature` file whose steps don't compile against `.steps.ts` |
| Framework lock-in | Pinning playwright-bdd and Playwright locator conventions matches the existing test stack |

---

## ▶️ Example Run

```bash
# Input
"As a clinician using the OncoKDM platform, I want to receive an automatic alert
when a sequencing result reveals an actionable variant, so that I can quickly
orient the patient toward an appropriate targeted treatment."
```

```
── Output (excerpt) ─────────────────────────────────────────
  summary               : Automatic alert for actionable variant detection
  iec62304_class        : C  (delayed/missed alert may cause serious patient harm)
  involves_phi          : true
  applicable_data_prot. : GDPR, HDS
  acceptance_criteria   : 5  (incl. AC-05 — audit trail on alert acknowledgement)
  test_cases            : 4  (happy_path · edge_case · error_case · security)
  gherkin_feature       : actionable-variant-alert.feature  (4 scenarios, tagged @TC-01…@TC-04)
  playwright_steps      : actionable-variant-alert.steps.ts (playwright-bdd, 100% step coverage)
```

---

## 📌 Roadmap

- [x] Base prompt — User Story → Jira ticket (BA + QA role)
- [x] Extended output — Gherkin feature + Playwright/TypeScript step definitions
- [x] HealthTech/MedTech adaptation — IEC 62304 classification, PHI detection, regulatory DoD
- [x] Token-optimized prompt (~45% fewer input tokens, identical output contract)
- [ ] Automated validation of generated `.feature` / `.steps.ts` pairs (lint + compile check)
- [ ] Direct Jira ticket creation via API
- [ ] Traceability matrix auto-export (CSV/Excel) for audit readiness
- [ ] Multi-language Gherkin support (currently English-only by design)

⭐ Star this repo to follow the progress.

---

## 👤 Author

**Khalid Hafid-Medheb**
Senior QA Automation Engineer — Regulated MedTech / Pharma — Playwright, Python, AI Testing

[![LinkedIn](https://img.shields.io/badge/LinkedIn-khalid--hafid--medheb-0077B5?style=flat-square&logo=linkedin)](https://www.linkedin.com/in/khalid-hafid-medheb-40451aa8/)
[![GitHub](https://img.shields.io/badge/GitHub-kallitests-181717?style=flat-square&logo=github)](https://github.com/kallitests)
[![Kallitests](https://img.shields.io/badge/Org-Kallitests-6e40c9?style=flat-square)](https://github.com/kallitests)

---

*Built with 🧠 Claude (Anthropic) · 🥒 Gherkin · 🎭 Playwright*
