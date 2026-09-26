# How to Work Through These Notebooks

A repeatable method for approaching each daily-practice notebook, based on the
pattern used across the pandas/statistics/ML-modelling/MLOps notebooks in this
repo.

## 1. Read before you code

- Read the **problem statement** in full before touching a single cell. Every
  notebook frames a fake but plausible workplace scenario (QA engineer,
  release gate, drift monitor, etc.) — that framing tells you *what a wrong
  answer looks like*, not just what function to write.
- Identify the **failure mode being tested**. These exercises are built around
  a specific way a naive approach breaks (accuracy hiding false negatives,
  aggregate MAE hiding a bad slice, a global median hiding a subgroup effect,
  a split-then-dedupe bug leaking test rows into train). Name that failure
  mode to yourself before writing code — it tells you what your solution must
  actually guard against, not just what output shape it must produce.
- Note anything **given as fixed**: function signatures, column names,
  constants (`RANDOM_STATE`, thresholds, URLs). Don't change these — later
  cells and the solution assume them.

## 2. Understand the data before the task

- Look at shape, dtypes, and a `.head()`/`.describe()` before writing any
  TODO. Know which columns have nulls, which are categorical vs numeric, and
  what the target represents (including any label-flip like
  `malignant == 1`).
- Note how the notebook already split the data (temporal split, stratified
  split, reference vs. production) — that split is usually the point of the
  exercise, not an incidental setup step.

## 3. Plan each TODO before implementing it

For every TODO cell:
1. Restate the spec in your own words: inputs, outputs, edge cases called out
   in the docstring/instructions (e.g. "nulls are not violations here",
   "duplicate_count is advisory only").
2. Decide the approach on paper/in a comment first when the logic has more
   than one moving part (e.g. group-wise imputation, PSI bucketing, a merge
   for leakage detection).
3. Implement, then immediately sanity-check the return type and shape against
   what the spec says (`list[dict]`, a specific set of keys, an `int`, a
   tuple of two frames) — a correct-looking function that returns the wrong
   shape will silently break every downstream cell.

## 4. Attempt it yourself before reading the solution

- Solutions are hidden under `<details>` for a reason — write your own
  implementation and run it against the notebook's own checks/assertions
  first.
- Only expand the solution after you have something that runs. Then diff your
  approach against it: same result, different method is fine and often more
  instructive than matching line-for-line.

## 5. Verify, don't just eyeball

- Re-run affected cells top to bottom after any change — state leaks between
  cells easily in notebooks.
- Cross-check numbers that should be invariant (row counts before/after
  cleaning, split proportions, that a "raw" gate fails and a "cleaned" gate
  passes).
- For anything involving randomness (bootstraps, CV, train/test splits),
  confirm you used the given `random_state`/`rng` so results are reproducible
  and comparable to the solution.

## 6. Answer the write-up honestly

The TODO write-ups ("would a CI gate of `accuracy > 90%` have caught this?",
"which slice fails the gate and why would aggregate MAE have missed it?") are
the actual deliverable — the code exists to produce the numbers that answer
them. Write the answer using your own numbers, not a generic restatement of
the problem statement, and be explicit about the practical consequence (what
ships, what doesn't, what a monitor should alert on).

## 7. General habits across all notebooks

- Don't over-engineer: implement exactly what the spec asks for in the given
  function signature — no extra parameters, no speculative generality.
- Handle the edge cases the instructions call out explicitly (empty groups,
  missing columns, single-class resamples) and no others.
- Keep cleaning/splitting/leakage-detection logic as separate, small,
  testable functions rather than one large script cell — that's the pattern
  every notebook already follows, and it's what makes the final "put it
  together" cell easy to reason about.
