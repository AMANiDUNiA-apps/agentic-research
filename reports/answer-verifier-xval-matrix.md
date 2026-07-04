# answer-verifier — cross-LLM verdict-reproduction matrix

**Skill:** `security/answer-verifier` · **Date:** 2026-07-04
**Free auto-verifier under test:** `nemotron-3-super-120b` (nvidia), timeout 900s
**Method:** each fixture answer + its ground-truth files were handed to the free model *with the
answer-verifier SKILL.md as its instructions*; it produced the ANSWER-VERIFY block unaided. No
internet. Verdicts compared to the Opus reference run (`answer-verifier-validation.md`).

## Result — VERDICT reproduced 5/5 (100%)

| Fixture | Opus verdict / sev | super-120b verdict / sev | Verdict match | Severity |
|---------|--------------------|--------------------------|---------------|----------|
| fixture-0-grounded    | PASS 0/8 | PASS 0/8 | ✅ | exact |
| fixture-3-unsupported | FLAG 3/8 | FLAG 3/8 | ✅ | exact |
| fixture-4-overclaim   | FLAG 4/8 | FLAG 4/8 | ✅ | exact |
| fixture-6-fabricated  | FAIL 6/8 | FAIL 5/8 | ✅ | −1 (same FAIL band) |
| fixture-8-confab      | FAIL 8/8 | FAIL 5/8 | ✅ | −3 (same FAIL band) |

**The install/trust decision (PASS / FLAG / FAIL) reproduced exactly on all five.** Severity was
exact on the PASS/FLAG cases; on the two FAIL cases the free model *under*-scored the integer (it
called both "fabricated reference / 5" rather than climbing to 6/8) but never mis-banded — a FAIL
stayed a FAIL.

## Reading
- **Gate on the verdict-band, not the raw integer** — same conclusion as the skill-auditor matrix
  ([[skill-auditor-llm-validation]]). For the answer-verifier the free model is *conservative* on
  the FAIL severity (opposite of the attack-pattern swarm, which over-anchored at 8); either way
  the integer drifts and the verdict holds.
- **super-120b is usable as the free auto-verifier** for the skills.j-amani.de gate: do-not-trust
  (FAIL) and trust (PASS) both reproduce; route FLAG to human review. Keep timeout ≥600s.
- The honesty rule survived delegation: the free model also returned the well-formed DOI in
  fixture-6 as unverifiable and pinned the fabrication on the provably-absent API method.

## Open
- Second-opinion / pre-filter model (`north-mini-code`, `ultra-550b`) not yet run — queued, as in
  the skill-auditor matrix. Raw runs: `~/files/inprogress/answer-verifier-xval/xv-*.md`.
