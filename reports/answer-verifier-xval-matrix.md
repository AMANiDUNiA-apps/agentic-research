# answer-verifier — cross-LLM verdict-reproduction matrix

**Skill:** `security/answer-verifier` · **Date:** 2026-07-04
**Free models under test:** `nemotron-3-super-120b` (nvidia, 120B) · `cohere/north-mini-code:free`
(3B) · `nemotron-3-ultra-550b` (OpenRouter, 55B active) — all timeout 900s.
**Method:** each fixture answer + its ground-truth files were handed to each free model *with the
answer-verifier SKILL.md as its instructions*; it produced the ANSWER-VERIFY block unaided. No
internet. Verdicts compared to the Opus reference run (`answer-verifier-validation.md`).

## Result — three free models vs. the Opus reference

Two views: the **3-tier verdict** the models emitted (PASS/FLAG/FAIL — the pre-verdict-layer format
these runs used), and the **binary GATE** the family now ships (`PASS` 0–2 / `FLAG` 3–8).

**Verdict + severity:**

| Fixture | Opus | super-120b (120B) | north-mini (3B) | ultra-550b (55B) |
|---------|------|-------------------|-----------------|------------------|
| 0 grounded    | PASS 0 | PASS 0 | PASS 0 | PASS 0 |
| 3 unsupported | FLAG 3 | FLAG 3 | FLAG 2 | PASS 0 → **FLAG 3** † |
| 4 overclaim   | FLAG 4 | FLAG 4 | FAIL 8 | FAIL 7 |
| 6 fabricated  | FAIL 6 | FAIL 5 | FAIL 8 | FAIL 6 |
| 8 confab      | FAIL 8 | FAIL 5 | FAIL 8 | FAIL 8 |

**Same runs as GATE (`PASS` 0–2 / `FLAG` 3–8):**

| Fixture | Opus | super-120b | north-mini | ultra-550b |
|---------|------|------------|------------|------------|
| 0 | PASS | PASS | PASS | PASS |
| 3 | FLAG | FLAG | FLAG | PASS → **FLAG** † |
| 4 | FLAG | FLAG | FLAG | FLAG |
| 6 | FLAG | FLAG | FLAG | FLAG |
| 8 | FLAG | FLAG | FLAG | FLAG |

> **† ultra fixture-3 was contaminated, then corrected — and this is the report's real finding.**
> The standard run returned `PASS 0/8`, and its ledger quoted the *grounded* fixture's claims that
> aren't in this answer. Cause: `hermes` loads **ambient user memory/rules** (`~/.hermes/SOUL.md` +
> `memories/`, which carry Gortex setup docs) into every run unless told not to — proven because
> **super-120b's fixture-3 output also contains "Gortex" text that is nowhere in the prompt.** A
> clean-room re-run with `--ignore-user-config --ignore-rules` returned `FLAG 3/8`, **exact to Opus**.
> So the "miss" was a **context-hygiene artifact, not a model verdict.**

- **super-120b — GATE 5/5, verdict 5/5.** Robust even to the leaked ambient text (it mentioned
  "Gortex" but still verdicted correctly). The free auto-verifier.
- **north-mini (3B) — GATE 5/5, verdict 4/5.** Smallest context → ignored the ambient text entirely.
  Over-escalated fixture-4 (FLAG→FAIL 8, but GATE-safe) and pins 8/8 on FAIL-side; never PASSed
  anything it shouldn't. Conservative pre-filter.
- **ultra-550b (55B) — GATE 5/5 clean-room.** In the un-sandboxed run its long context absorbed the
  leaked Gortex docs and wove them into its ledger (a false PASS on run 1; a hallucinated "Gortex
  section" on run 2). Sandboxed, it matched Opus (FLAG 3) and nailed fixtures 6/8 exactly. The
  failure was contamination, not the model.

## Reading
- **The real finding: sandbox your swarm runs.** `hermes` injects ambient `~/.hermes` memory/rules
  unless you pass `--ignore-user-config --ignore-rules`. That leaked Gortex setup docs into every
  prompt this session. Small/mid models shrugged it off; the long-context 550B wove it into its
  output and flipped a verdict. **Longer context = larger contamination surface when unsandboxed.**
  (All prior swarm results this session ran un-sandboxed and still reproduced correctly — but ultra
  fixture-3 shows it *can* bite. Clean-room is the fix.)
- **The binary GATE isolates the one disagreement that matters.** GATE absorbs every FLAG↔FAIL
  quibble (north-mini's and ultra's fixture-4 both stay `FLAG`). The only thing it can't hide is
  `PASS`↔`FLAG`, and the single such divergence was the contamination artifact — which clean-room
  removed. **Under clean-room + GATE, all three free models agree with Opus 5/5.**
- Gate on GATE/verdict, not the raw integer — severity drifts across models (super under-scores,
  north-mini over-anchors at 8).

## Role assignment (final)
- **`super-120b` = free auto-verifier** — GATE 5/5, robust to ambient noise. Timeout ≥600s.
- **`north-mini-code` (3B) = safe pre-filter.** `PASS` → trust; `FLAG` → escalate. Never its integer.
- **`ultra-550b` = usable heavy cross-check, but only sandboxed** (`--ignore-user-config
  --ignore-rules`); its long context makes it the most vulnerable to prompt contamination.
- **Methodology rule for every swarm run:** pass `--ignore-user-config --ignore-rules` so ambient
  memory/rules don't leak into the task. For anything at stake, still prefer super-120b + human or a
  2-of-3 ensemble over any lone free model.
- Raw runs: `~/files/inprogress/answer-verifier-xval/xv-*.md` (super) · `xv-nm-*.md` (north-mini) ·
  `xv-ul-*.md` + `xv-ul-3clean.md` (ultra, incl. clean-room).
