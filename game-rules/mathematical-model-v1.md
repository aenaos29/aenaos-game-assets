# EMMA vs AENAOS — Locked Mathematical Model v1.0

**Game:** EMMA vs AENAOS — The Quantisation Trial  
**Version:** v1.0-locked  
**Date:** 2026-11-28  
**Status:** Locked after simulation review

## Scope

This document defines the randomised trial-level coefficients used by S01. It does not replace the canonical case data, evidence weights, inference weights, links, thresholds, settlement rules, or verdict rules. Those remain authoritative in the case data.

The player may choose questions without knowing which question has the highest effective weight.

## 1. Canonical score budget

The total score ceiling remains **100.00**:

| Component | Ceiling |
|---|---:|
| Canonical inferences / arguments | 50.00 |
| Evidence items | 20.00 |
| Context links | 10.00 |
| Speeches | 20.00 |
| **Total** | **100.00** |

There are 90 active canonical inferences. Deduplicate activated inference IDs before scoring.

## 2. Trial-start randomisation

At the beginning of every new trial, generate and persist one random coefficient for each applicable object. The coefficients remain fixed for that trial.

### Common juror and lawyer coefficients

For each juror and for both lawyers:

```text
C_common ~ Uniform(0.90, 1.10)
```

The common coefficient is not shown to the player.

### Witness coefficients

For every witness and lawyer side:

```text
C_witness[witness][side] ~ Uniform(0.70, 1.30)
```

Witness coefficients are lawyer-specific. The coefficient for LEX does not equal the coefficient for ARTHUR unless randomisation happens to produce the same value.

## 3. Question selection and effective value

The player selects from the available questions without seeing the hidden effective value.

For a question with canonical signed value `q`:

```text
effectiveQuestionValue =
  q
  × C_common[side]
  × C_witness[witness][side]
  × C_position[side, witness, question]
```

`C_position` is the bounded position/interaction modifier defined by the trial implementation. It must not create a new score budget or alter the canonical sign of the question.

The canonical value remains the source of truth for the question's legal direction. Randomisation changes influence, not which side the question supports.

## 4. Objections

Each side has two objection attempts per witness.

- **SUSTAINED:** the question is cancelled before answer processing. It contributes no answer, stress, inference activation, link activation, or question points.
- **OVERRULED:** the question proceeds normally and its effective value is counted normally. The ruling also strengthens the affected witness by the configured percentage for the remainder of the applicable block.

Both outcomes consume one objection attempt.

No objection creates a new independent score category.

## 5. Opportunity limits

For each witness:

- Each side may answer at most five questions.
- A SUSTAINED question consumes the relevant opportunity but contributes zero score.
- A voluntary PASS ends that side's remaining opportunities for the current witness.
- The combined rule remains: `answered + sustained-cancelled <= 5` per side.

## 6. Preservation rules

The following are unchanged:

- 90 canonical inferences and the 50.00 inference ceiling.
- Evidence, context-link, and speech budgets.
- Existing signed weights and links.
- Deduplication by activated inference ID.
- Settlement mathematics.
- Nine-juror verdict calculation.
- Civil standard: preponderance of the evidence.
- No visible indication of the highest-weight question.
- No bonus category added for witnesses, lawyers, position, or objections.

## 7. Reproducibility

A test run must record:

- random seed;
- trial ID;
- generated common coefficients;
- generated witness-by-side coefficients;
- position modifiers;
- question order and selections;
- objections and rulings;
- activated inference IDs;
- final score and verdict.

A replay with the same seed and identical actions must reproduce the same result.

## 8. Boundary requirements

Implementations must verify:

```text
0.90 <= C_common <= 1.10
0.70 <= C_witness <= 1.30
```

Values outside these intervals are invalid. Random coefficients must be generated once per trial, not regenerated after every question.

The randomisation layer must never change canonical data or make a previously cancelled question scoreable.

## 9. Interpretation

The model intentionally permits a player to select a question that is not the strongest hidden option. Strategy is based on evidence, witness context, objections, and legal reasoning—not on access to hidden numerical weights.

The model is locked for S01. Any future changes belong to a new model version and must not silently alter S01 replays.
