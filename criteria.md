# Acceptance criteria — TakeMeter

Five criteria that say what "working" means for this classifier, written in
unit 5 **before** anything was trained.

**All five are yours this time.** None are given. You've had two projects of
practice.

An acceptance criterion names a number. *"The model is accurate"* is an
opinion. *"Every label has an F1 of at least 0.60 on the held-out set"* is a
criterion.

Under each, write a sentence or two on **why that number**. A reason that says
something about your data or your taxonomy earns credit — *"I picked 0.60 F1
for `reaction` because it's my smallest label and I only have about 50
examples of it"*. A reason that could be attached to any project does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## Pick numbers you can defend

Not numbers that sound impressive. Three labels means a coin-flip guesser gets
about 33%, so a target of 0.40 is barely a target. Your number should sit
somewhere you'd honestly call useful.

**Cover at least three of these five areas.** They're here as prompts, not as a
form to fill in — a criterion that fits none of them is fine if it names a
number.

| Area | A question it could answer |
|---|---|
| Overall accuracy | How often does it need to be right to be worth using? |
| Per-label performance | Is one label allowed to be much worse than the others? |
| Balance | How lopsided can your label counts get before it's a problem? |
| Consistency | If someone else labelled the same posts, how often should you agree? |
| Confidence | Should a confident prediction be right more often than an unsure one? |

Two things worth knowing before you pick numbers, because both will affect
whether you hit them:

- **Your smallest label will have the jumpiest score.** If a label has 50
  examples, about 8 land in the test split. An F1 computed on 8 examples moves
  a lot between seeds. A target for that label should be looser than one for
  your biggest label, and saying so is a good reason.
- **Unit 6 tests across three seeds, and the target has to hold across all
  three.** A target of 0.65 against results of 0.71, 0.62, 0.68 is a **miss**.
  Pick with that in mind — it is stricter than it first sounds.

---

## 1.

<!-- Your criterion. It must name a number. -->

Overall accuracy on the held-out test split is at least **0.62** across all three seeds (`42`, `7`, and `2024`).

**Why this target:**

With three labels (`analysis`, `hot_take`, `reaction`), random guessing gets ~0.33 and always guessing my largest `r/soccer` class (`hot_take`, ~40–45% of posts) gets ~0.45, so 0.62 proves the model learned real distinctions beyond the majority class. Because the target must hold across all three seeds in Unit 6 without a single dip on a 30-row test split, 0.62 is strict enough to be useful without failing on a one-post seed fluctuation.

---

## 2.

<!-- Your criterion. -->

The `reaction` label achieves an F1 score of at least **0.65** on the held-out test split across all three seeds.

**Why this target:**

Live `r/soccer` match-thread reactions (e.g., `"GET IN!!"`, `"Robbed by VAR!"`) are short, heavily punctuated exclamations that are structurally distinct from multi-clause arguments and well-represented in my dataset (~65–70 examples, ~10 in the test split). Because `reaction` has the cleanest boundary in my taxonomy and more test examples than `analysis`, it should reliably clear a higher 0.65 F1 bar on every seed.

---

## 3.

<!-- Your criterion. -->

The `analysis` label achieves an F1 score of at least **0.55** on the held-out test split across all three seeds.

**Why this target:**

Substantive tactical or statistical posts citing checkable facts (`xG`, possession percentages, or setups like `"parking the bus"`) are my smallest class in `r/soccer` (~50–55 examples in `labels.csv`, leaving only ~8 examples in the 15% test split). On 8 test examples, misclassifying a single borderline `analysis` post as `hot_take` moves F1 by ~0.10 between seeds, so a looser 0.55 target accounts for small-sample variance while still ensuring the model surfaces real analysis.

---

## 4.

<!-- Your criterion. -->

No single label makes up more than **45%** of `labels.csv` (and the smallest label has at least **50 examples**).

**Why this target:**

Unfiltered `r/soccer` post-match threads are over 70% knee-jerk `hot_take` and `reaction` comments, which is much worse than the project's 70% cap and would leave fewer than 8 `analysis` posts in the test split. Capping my largest class at 45% and collecting at least 50 `analysis` posts prevents the model from lazily guessing `hot_take` and ensures every label has enough test examples to measure in Unit 6.

---

## 5.

<!-- Your criterion. -->

Human annotation agreement on the 30-post calibration set (`agreement.py`) is at least **75%** (at least **23 out of 30** posts).

**Why this target:**

My hardest boundary in `r/soccer` is `analysis` vs. `hot_take`, where heated comments mix a sweeping opinion with a brief match detail, and the 30 calibration posts intentionally include ambiguous borderline cases. Agreeing on at least 75% (23/30) of posts verifies that a human annotator can apply written label definitions consistently before expecting a fine-tuned model to learn the boundaries.

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 6 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath, like this:

         ## 2. Every label performs acceptably

         The model performs well on all labels.

         **Why this target:** ...

         > **Revised in unit 6:** Every label has an F1 of at least 0.60 on
         > the held-out set.
         >
         > **Why revised:** "performs well" gave me nothing to check. I
         > couldn't produce a verdict from it at all.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "Overall accuracy of at least 0.65" → "at least 0.55", because
            0.65 turned out to be optimistic for 200 examples.

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.
     ───────────────────────────────────────────────────────────────────────── -->
