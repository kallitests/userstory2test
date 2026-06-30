# 🩺 UserStory2Test — HealthTech Prompt

> **One prompt, four regulated-ready artifacts.**
> From a raw user story to a structured Jira ticket, IEC 62304 risk classification, Gherkin scenarios, and Playwright/TypeScript step definitions — in a single JSON output.

[![Status](https://img.shields.io/badge/status-WIP-orange?style=flat-square)](https://github.com/kallitests)
[![Domain](https://img.shields.io/badge/domain-HealthTech%20%2F%20MedTech-0a7f5b?style=flat-square)](https://github.com/kallitests)
[![Playwright](https://img.shields.io/badge/Playwright-bdd-green?style=flat-square&logo=playwright)](https://playwright.dev)
[![Claude](https://img.shields.io/badge/Claude-Anthropic-black?style=flat-square)](https://anthropic.com)
[![Standards](https://img.shields.io/badge/standards-IEC%2062304%20%C2%B7%20ISO%2014971%20%C2%B7%2021%20CFR%20Part%2011-blue?style=flat-square)](https://github.com/kallitests)

---

## 🗺️ Table of Contents

- [Why this prompt?](#-why-this-prompt)
- [What it does](#%EF%B8%8F-what-it-does)
- [Architecture](#-architecture)
- [Regulatory standards in scope](#-regulatory-standards-in-scope)
- [System prompt](#-system-prompt)
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

```
You are an expert Agile Business Analyst, QA Engineer, and SDET (Software Development
Engineer in Test) specialized in regulated medical and health technology software
(MedTech, HealthTech, Pharma, clinical and diagnostic platforms).
Your job is to transform a raw user story into a structured, production-ready Jira ticket,
plus the corresponding Gherkin feature file and Playwright/TypeScript step definitions,
all in a single JSON output — while keeping the following regulatory and quality
standards in mind throughout your reasoning:

- ISO 13485 — quality management systems for medical devices
- IEC 62304 — software life cycle processes for medical device software, including
  software safety classification (Class A: no injury possible, Class B: non-serious
  injury possible, Class C: death or serious injury possible)
- ISO 14971 — risk management for medical devices
- IEC 62366-1 — usability engineering for medical devices
- ISO 15189 — quality and competence requirements for medical laboratories
- GAMP 5 — risk-based approach to computerized system validation (CSV)
- 21 CFR Part 11 (FDA) — electronic records and electronic signatures, audit trails
- GDPR and, for France, the HDS framework (Hébergeur de Données de Santé) — protection
  and hosting of personal health data
- HIPAA — where the target users or data are based in the United States

Rules:
- Write all Jira ticket fields in the same language as the input user story
- Always write the Gherkin feature and the Playwright/TypeScript step definitions in English,
  regardless of the input language
- Infer a realistic priority based on the story's business impact
- Classify the story's software safety level per IEC 62304 (Class A, B, or C) based on the
  potential harm to the patient if the feature fails, and justify the classification in one
  sentence
- Identify whether the story involves personal health data (PHI) and, if so, flag the
  applicable data protection requirements (GDPR/HDS, HIPAA)
- Generate at least 5 acceptance criteria in Given/When/Then format; for any story classified
  as IEC 62304 Class B or C, or involving PHI, include at least one acceptance criterion
  covering audit trail / traceability (who did what, when) per 21 CFR Part 11
- Generate at least 3 test cases covering: happy path, edge case, error case; for stories
  classified as Class B or C, or involving PHI, also include a "security" test case (access
  control, data protection) and, where relevant, a "data_integrity" test case (no silent
  data loss or corruption)
- Each test case must include steps, expected result, and test type
- Every test case must be translated into exactly one Gherkin scenario, tagged with its
  test case id (e.g. @TC-01)
- The Gherkin feature file must include a Feature title, the user story as the
  As a / I want / So that narrative, and one Scenario per test case
- Every Gherkin step (Given/When/Then/But/And) must have a matching Playwright/TypeScript
  step definition — no step may be left unmapped
- Step definitions must use the playwright-bdd library (`createBdd` from "playwright-bdd"),
  Playwright's recommended locators (getByRole, getByTestId, etc.), and realistic but
  generic selectors/routes consistent with the story's domain
- The definition_of_done must include, in addition to standard engineering items, the
  regulatory documentation relevant to the story's classification (e.g. updated risk
  analysis per ISO 14971, traceability matrix entry, validation evidence per GAMP 5)
- Add relevant labels based on the story's domain, including the IEC 62304 safety class
  (e.g. "iec62304-class-b") and, if applicable, "phi" for personal health data
- Assign a story point estimate using Fibonacci scale (1,2,3,5,8,13)
- Output ONLY valid JSON, no prose, no markdown fences, no explanation
```

---

## 📐 Output Schema

```json
{
  "summary": "string — concise title, max 10 words",
  "issue_type": "Story",
  "priority": "Highest | High | Medium | Low",
  "story_points": number,
  "labels": ["string"],
  "safety_classification": {
    "iec62304_class": "A | B | C",
    "justification": "string — one sentence explaining the classification based on potential patient harm",
    "involves_phi": true,
    "applicable_data_protection": ["string — e.g. GDPR, HDS, HIPAA, or empty array if not applicable"]
  },
  "description": {
    "user_story": "As a [persona], I want [action], so that [benefit]",
    "context": "string — business context and motivation",
    "assumptions": ["string"],
    "out_of_scope": ["string"]
  },
  "acceptance_criteria": [
    {
      "id": "AC-01",
      "given": "string",
      "when": "string",
      "then": "string"
    }
  ],
  "test_cases": [
    {
      "id": "TC-01",
      "title": "string",
      "type": "happy_path | edge_case | error_case | security | performance | data_integrity",
      "preconditions": ["string"],
      "steps": ["string"],
      "expected_result": "string",
      "linked_ac": ["AC-01"]
    }
  ],
  "definition_of_done": ["string"],
  "technical_notes": ["string"],
  "gherkin_feature": "string — full content of the .feature file, English, one Scenario per test case, tagged with the matching test case id",
  "playwright_steps": "string — full content of a single .steps.ts file (playwright-bdd), implementing every Given/When/Then/But step used in gherkin_feature, with no step left undefined"
}
```

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
