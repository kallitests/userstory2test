# UserStory2Test — Project Spec

**One-line pitch:** A single AI prompt that turns a raw user story into a regulated-ready Jira ticket, Gherkin scenarios, and Playwright/TypeScript step definitions — in one pass.

---

## Problem

In regulated MedTech/HealthTech teams, a user story rarely becomes a test-ready ticket on the first try. Today, getting from "raw idea" to "automated, auditable test" typically requires four separate manual steps, each with its own failure mode:

| Pain point | Impact |
|---|---|
| **Manual ticket drafting** | BAs spend time writing acceptance criteria and test cases by hand for every story — slow, inconsistent quality, easy to forget edge cases. |
| **Risk classification is an afterthought** | IEC 62304 software safety class (A/B/C) and PHI/data-protection flags are usually assessed late, often during a compliance review — not at ticket creation time. |
| **Gherkin written separately from Jira** | QA rewrites the same scenarios in `.feature` files days later, introducing drift between what the ticket says and what the tests actually check. |
| **Automation code written separately from Gherkin** | SDETs then hand-map Gherkin steps to Playwright code, frequently leaving steps unimplemented or out of sync with the scenario file. |
| **Regulatory documentation bolted on at the end** | Risk analysis updates, traceability matrix entries, and validation evidence are produced reactively before an audit, instead of being part of the Definition of Done from day one. |
| **Verbose prompts cost real money at scale** | A naively written prompt re-explains every standard and duplicates field semantics between the rules and the schema on every single call — fine for a one-off, expensive once a team runs it on every story in every sprint. |

The result: slow ticket-to-test cycles, inconsistent coverage across stories, and compliance documentation that is assembled under time pressure rather than built in.

---

## Solution

**UserStory2Test** is a single, constrained LLM prompt (Role + Instructions + Schema + Input format) that takes one raw user story as input and returns one JSON object containing everything a regulated team needs to start work immediately:

- A **production-ready Jira ticket** (summary, priority, story points, acceptance criteria, definition of done) written in the input language.
- An **IEC 62304 safety classification** (Class A/B/C) with a one-sentence justification based on potential patient harm — generated automatically, not assessed later.
- **PHI detection** with the applicable data protection regime flagged (GDPR/HDS, HIPAA) as soon as the story is drafted.
- At least **3 test cases** (happy path, edge case, error case), automatically expanded with security and data-integrity cases for higher-risk or PHI-involving stories.
- A **Gherkin `.feature` file** in English, with exactly one tagged scenario per test case (`@TC-01`, `@TC-02`…), keeping Jira and Gherkin in sync by construction.
- Matching **Playwright/TypeScript step definitions** (`playwright-bdd`), with every Gherkin step mapped to working code — no step left undefined.
- A **regulatory-aware Definition of Done**, referencing the actual artifacts an auditor expects: updated risk analysis (ISO 14971), traceability matrix entry, validation evidence (GAMP 5).

The prompt encodes the standards a regulated team already has to think about — ISO 13485, IEC 62304, ISO 14971, IEC 62366-1, ISO 15189, GAMP 5, 21 CFR Part 11, GDPR/HDS, HIPAA — so the output speaks the right vocabulary from the first draft, rather than needing translation into compliance terms afterward.

The prompt itself is **token-optimized**: standards are referenced by code rather than re-explained, rules are written as dense numbered instructions instead of verbose bullets, and the schema uses compact type/enum notation instead of duplicating field descriptions already covered by the rules. This cuts the system prompt by roughly 45% (≈870 → ≈480 tokens) with an identical behavioral contract — same required fields, same conditional logic, same Gherkin/Playwright coverage guarantee. At team scale (one call per story, every sprint), this directly reduces the recurring cost of running the prompt.

---

## What it is not

UserStory2Test is a **drafting accelerator**, not a compliance authority. Every safety classification, risk justification, and piece of generated documentation is designed to be reviewed and signed off by a qualified Quality/Regulatory Affairs role before being treated as actual regulatory evidence.

---

## Outcome

One user story in → one Jira ticket, one risk classification, one Gherkin file, and one executable Playwright test suite out — all traceable to each other by design, at roughly 45% less prompt-token cost than a naive implementation, in the time it takes to run a single prompt.