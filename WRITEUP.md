# Proposal triage design

## Process and assumptions

An admin configures a category's required fields, mandatory checks, limits, scoring bands and weights. The reviewer selects the category and uploads proposals. The system stores a batch with an immutable criteria version, validates the files and extracts text. Unreadable text goes to manual review. AI converts readable text into evidence-linked facts. Code validates the response, checks eligibility and calculates a ranked result. A reviewer checks the evidence and makes the final award decision.

For this exercise, I treat A and E's "since 2021" experience as the intended five-year evidence. In production, the category would define an assessment date and exact lookback dates. C's total begins in 2020, so its qualifying count is unknown and needs clarification. A statement that a lead is named counts as present in these summaries; the actual document would need supporting details.

I treat the budget ceiling and 16-week limit as eligibility constraints: an explicit breach is ineligible; an unknown value is flagged. Missing mandatory evidence is flagged unless the stated arrangement contradicts the requirement, as in B. All credentials are self-reported until the reviewer verifies them.

## 1. AI vs. rules

AI extracts values and supporting text because proposals describe the same facts in different ways. It does not decide eligibility, assign points or rank bidders. C# code checks the JSON schema, applies admin-configured rules and calculates the result. This keeps arithmetic and policy decisions predictable. A simple implementation could use ASP.NET Core, SQL Server and one LLM extraction service;

## 2. Consistency and explainability

Each weighted criterion receives 0-5 points using the rubric below. Contribution = points / 5 x weight. The final score is the sum, rounded to two decimals after calculation. Higher scores rank first; ties share a rank and require human comparison.

| Criterion | Weight | Proposed admin-configured points |
| --- | --- | --- |
| Relevant experience | 30% | Qualifying projects: 0 = 0; 1-4 = 2; 5-9 = 4; 10+ = 5. Unknown count: flag. |
| Price | 25% | Percentage of budget: up to 90% = 5; over 90% through 95% = 4; over 95% through 100% = 3. Over budget: ineligible. Missing: flag. |
| Timeline | 15% | Up to 12 weeks = 5; over 12 through 14 = 4; over 14 through 16 = 3. Over 16: ineligible. Missing: flag. |
| Warranty | 15% | Panel warranty: 25+ years = 2, 20-<25 = 1, shorter = 0. Workmanship: 12+ years = 3, 10-<12 = 2, 5-<10 = 1, shorter = 0. Add both. Missing duration: flag. |
| Team and approach | 15% | 1.25 each for a stated named lead, identified crew, safety plan and monitoring plan. Undocumented items receive 0; conflicting evidence is flagged. |

These are proposed scoring bands, not formulas supplied by the brief. All thresholds, fields, weights and mandatory conditions belong to category configuration, never category-specific controller logic. Invalid values and weights not totaling 100% prevent scoring.

| Proposal | Experience | Price | Timeline | Warranty | Team | Total |
| --- | --- | --- | --- | --- | --- | --- |
| A | 30 | 20 | 15 | 12 | 15 | 92 |
| E | 24 | 20 | 12 | 15 | 15 | 86 |

A and E both fall in the same price band. A's experience and timeline advantage exceeds E's warranty advantage. B is ineligible because a future licensed subcontractor does not satisfy the bidder-held licence rule. C is flagged for its uncertain qualifying experience count; its embedded instruction is separately logged. D is flagged because its schedule cannot be checked. Neither flagged proposal receives a final score until clarified.

The same confirmed facts and criteria version produce identical scores. A fresh AI extraction is not guaranteed to match, even at low temperature. Store the original extraction and approved corrections, with document hash, prompt version and model identifier. Show each value, source evidence, rule, points and weighted contribution beside the result.

## 3. Human in the loop

A reviewer resolves missing or conflicting facts, checks mandatory evidence and reviews injection warnings. Corrections rerun the same rules. A final selection or override requires a reason; it does not overwrite the calculated score. Log the actor, timestamp, original and corrected values, evidence, criteria/prompt/model versions, score breakdown and award decision. A ranking never automatically awards a contract.

## 4. Changing criteria

An admin edit creates a new version. Existing results keep their old version and are marked as based on older criteria. A reviewer explicitly reruns the whole batch against the new version; results from different versions are not mixed. Reuse approved facts if fields are unchanged. Extract and review additional facts when a new criterion requires them. Preserve both result sets for comparison.

## 5. Risks

Wrong extraction or invented facts: require evidence, schema validation and null for unknowns. Check evidence against source text; a matching quote still needs review for meaning. Bias: use the same factual rubric and exclude company names from scoring. Manipulation: treat uploads as untrusted data, give the model no tools and ignore embedded instructions; log C's warning for review. Missed attacks remain possible, so code alone controls scores. Cost and failures: limit file size, pages and model output, cache extraction, allow one bounded retry, then send failures to manual review. Restrict document access to authorised users and log changes.
