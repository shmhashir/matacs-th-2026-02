# AI-Assisted Proposal Triage

- Full name: **Shaikh Muhammad Hashir**
- Assessment reference: **MATACS-TH-2026-02**
- Actual time spent: **1.6 hours**

This is a design for a proposal review tool. AI extracts information, a rule based service checks eligibility and calculates scores, and a person makes the final award decision. No deployed application or working prototype is included.

## Files

- [WRITEUP.md](WRITEUP.md): process, scoring rubric, assumptions and answers to all five questions.
- [Process diagram](diagram/process-flow.png): AI steps, rules and human checkpoints.
- [PROMPTS.md](PROMPTS.md): extraction messages and expected response format.
- [ranking.json](ranking.json): expected results for all five proposals.

## Expected result

| Proposal | Rank | Status | Score |
| --- | --- | --- | --- |
| A | 1 | ranked | 92 |
| E | 2 | ranked | 86 |
| B | - | ineligible | - |
| C | - | flagged | - |
| D | - | flagged | - |

These are triage results, not a contract award. C needs confirmation of its qualifying experience count, and D needs a completion duration. The instruction embedded in C cannot change its score or rank.

## AI usage disclosure

I used ChatGPT to draft the process, scoring rubric, extraction prompt and supporting files. At once Ai suggested me to connect it with direct uploads or allow take decisions if something out of the box written in that proposal. I write prompts with the details and use ai to make it complete and detailed and then review the prompt again so that there is no chance of any mistake, I use AI that if i take that decision will it make any issue or something and review it accordingly with the reasons.
