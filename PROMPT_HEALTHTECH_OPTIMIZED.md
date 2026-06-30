# Prompt — User Story to Jira Ticket + Gherkin + Playwright/TypeScript (HealthTech / MedTech) — Token-Optimized

This is a token-optimized version of the HealthTech/MedTech prompt. Same output contract
(Jira ticket + IEC 62304 classification + Gherkin + Playwright/TypeScript), rewritten for
minimum input-token cost: standards are referenced by code only (no embedded definitions),
rules are merged into dense single-line instructions, and the schema drops descriptive
comments in favor of type/enum hints only. Estimated ~45% fewer tokens than the original
while preserving every rule and every output field.

---

## System prompt

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

## What changed vs. the original (and why it still works)

| Change | Token saving | Why it's safe |
|---|---|---|
| Standards listed as codes only, no inline definitions | ~120 tokens | The model already knows what ISO 13485/IEC 62304/etc. mean; the original definitions were for human readers of the README, not needed by the model to follow the rules correctly. |
| Numbered, single-line rules instead of bulleted prose | ~150 tokens | Same instructions, denser phrasing; numbering also makes the rule set easier to reference/debug than long bullets. |
| Schema without per-field comments | ~180 tokens | Field semantics are already fully specified in the rules above; repeating them in the schema was pure duplication. |
| Inline enums (`"A\|B\|C"`) instead of spelled-out options | ~20 tokens | Same constraint, shorter syntax — still prevents hallucinated enum values. |
| Dropped restated examples (e.g. `"iec62304-class-b"` kept once, not repeated) | ~15 tokens | One example is enough to fix the format; repeating it added no new constraint. |

**Net effect:** the original system prompt is ~870 tokens; this version is ~480 tokens
(≈45% reduction), with identical behavioral contract — same required fields, same
conditional rules (Class B/C or PHI → extra AC + extra test cases), same Gherkin/Playwright
coverage guarantee.

---

## Output-side token savings (optional, ask explicitly if needed)

If you also want to cut **output** tokens (not just input/prompt tokens), consider asking
the model for a `compact` mode in a follow-up turn rather than baking it into this prompt
(since it trades away information you may still want for audit purposes):

- Drop `technical_notes` and `assumptions`/`out_of_scope` if not used downstream.
- Cap `gherkin_feature`/`playwright_steps` to only the `Background` + first scenario for a
  quick preview, then request the full file in a second call once the ticket shape is approved.
- Request `test_cases[].preconditions` and `.steps` as short phrases instead of full sentences.

These are **not** included in the prompt above by default, because they reduce the
regulatory completeness of the ticket — cut them only when you explicitly don't need that
information for the task at hand.

---

## Note on regulatory scope

This prompt is a drafting aid, not a compliance certification. The IEC 62304 classification,
risk justification, and generated documentation must be reviewed and signed off by a
qualified Quality/Regulatory Affairs role before being used as actual regulatory evidence.
