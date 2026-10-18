# TakeMeter

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, the
> notebook, the baseline, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> head -5 data/practice_labels.csv     # the shape your labels.csv needs
> ```
>
> Then open `takemeter.ipynb` **in this folder** — in VS Code, or with
> `jupyter notebook` if you prefer. Pick the kernel: the `.venv` inside this
> project. Run section 1, which reports the hardware you'll be training on.
> Everything else waits until you have data.
>
> Nothing to upload, nothing to connect, no accounts and no keys. The notebook
> runs on your machine and writes next to your code.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     Unit 5 asks for the first five sections. Unit 6 adds the five below them.

     Everything is pasted as TEXT. No screenshots, no images.

     ⚠️ The confusion matrix especially. The notebook prints one as a markdown
     table, ready to copy. A screenshot of a matrix earns nothing. Paste the
     table.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 5 — THE BUILD ═══════════════════════ -->

## What This Does

<!-- Your community, and what your classifier sorts posts into. Three or four
     sentences. -->

TakeMeter is a text classifier built for public post-match and daily discussion threads on Reddit's `r/soccer` community. It sorts community comments into three distinct categories—`analysis`, `hot_take`, and `reaction`—so readers can filter out emotional match-thread noise and easily find substantive tactical or statistical discussions. While reading through 35+ real comments in `r/soccer`, I observed four concrete text distinctions that shaped these categories:

1. **Checkable metrics/facts vs. unbacked claims:** Analytical posts cite verifiable match details (such as expected goals `xG`, possession stats, match minutes, or specific tactical setups like "parking the bus"), whereas hot takes state sweeping conclusions without supporting evidence.
2. **Causal tactical reasoning vs. raw emotional venting:** Substantive comments connect an observation to a cause ("Because the fullbacks pushed too high, they were exposed on the counter-attack"), while match-thread reactions are immediate emotional outbursts ("GET IN!!", "Robbed by VAR again!").
3. **Evaluative claims about trajectory vs. single-moment gut feelings:** Hot takes make a bold declarative claim about a player's legacy, form, or a manager's future (e.g., debating Lionel Messi's current form), whereas live reactions only express excitement or frustration about an event that happened seconds ago.
4. **Multi-sentence structured arguments vs. one-line assertions:** Analytical comments typically span multiple clauses contrasting tactics or statistics, while hot takes and reactions are usually short, single-sentence declarations or exclamations.


---

## Label Taxonomy

### `analysis`

**Definition:** Makes an argument backed by a specific, checkable fact, stat, or tactical detail.

**Example 1:**
> "Because the fullbacks pushed too high, they were constantly exposed on the counter-attack, leading to that second goal."

**Example 2:**
> "His xG is 0.84 this match, but his finishing has been completely off target every time he gets inside the box."

### `hot_take`

**Definition:** A confident evaluative claim or conclusion with no support or evidence offered.

**Example 1:**
> "He is completely washed and should never start for this club again."

**Example 2:**
> "Worst manager in the league by a mile, absolutely clueless tactics."

### `reaction`

**Definition:** An immediate, emotional outburst or response to an event that just happened.

**Example 1:**
> "GET IN!!! WHAT A GOAL!"

**Example 2:**
> "Absolute robbery, how is that a red card?!"

### The hardest boundary

**Which two labels:** `analysis` vs. `hot_take`

**The decision rule I used every time:**
If the post names at least one specific, checkable match fact (like a stat, player position, or tactical setup) to support its point, it is `analysis`, even if the tone is highly opinionated or heated. If it only contains sweeping conclusions without citing any tangible evidence, it is a `hot_take`.
---

## The Dataset

**Where the posts came from:**
I collected all 200 posts from the "Daily Discussion" and "Post-Match" threads in the `r/soccer` subreddit during October 2026.

**How I labelled them:** 
I cold-labelled the first 20 examples manually without any AI assistance. For the remaining 180 examples, I used an AI assistant to pre-label batches of comments. I then manually read and verified every single pre-labelled post to ensure it correctly aligned with my written taxonomy before adding it to my `labels.csv` file.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `analysis` | 68 | 34.0% |
| `hot_take` | 68 | 34.0% |
| `reaction` | 64 | 32.0% |
| **Total** | 200 | 100% |

**Three hard cases**

**1.**
> *The post:* "They only won 40% of their ground duels, getting completely overrun in the center. Absolute joke of a midfield."
>
> *Could have been:* `analysis` or `hot_take`
>
> *I chose, because:* `analysis`, because it cites a specific checkable statistic (40% ground duels won) to form the basis of its argument. Even though it ends with a highly opinionated hot-take tone, the presence of the checkable fact triggers the rule for analysis.

**2.**
> *The post:* "I cannot believe he missed that! Open goal!"
>
> *Could have been:* `reaction` or `hot_take`
>
> *I chose, because:* `reaction`, because it is an immediate, highly emotional exclamation reacting to an event (a missed chance) that just happened seconds ago, rather than a broad evaluative claim about the player's overall career or abilities.

**3.**
> *The post:* "Tuchel has no idea how to build an attack, he just relies on individual brilliance."
>
> *Could have been:* `hot_take` or `analysis`
>
> *I chose, because:* `hot_take`, because even though it discusses "building an attack," it provides no specific tactical details, formations, or stats to back up the sweeping claim. It is entirely an unbacked evaluative conclusion.

---

## The Training Run

**Base model:**
`distilbert-base-uncased`

**Settings:** 
EPOCHS = 3, LEARNING_RATE = 2e-5, BATCH_SIZE = 16, SEED = 42

**Anything I changed from the defaults, and why:**
I left the hyperparameters at their default values because the dataset is small (200 rows) and the default settings were sufficient to achieve a 1.000 (100%) training accuracy and a 0.900 test accuracy by Epoch 3 without overfitting early. 

**Split sizes:** 
Train: 139 / Validation: 31 / Test: 30. 
In the test split, the labels were perfectly balanced: `analysis` had 10, `hot_take` had 10, and `reaction` had 10. Since no label had fewer than 8 examples in the test split, the metrics shouldn't be overly jumpy in next unit's variance tests.

---

## How I Used AI
## How I Used AI

**Moment 1**

- *What I asked for:* I asked the AI to check my `criteria.md` and `README.md` files for grammar and spelling mistakes.
- *What came back:* The AI returned my text with a few corrected words and better punctuation.
- *What I changed:* I accepted the grammar fixes to make my writing clearer, but I kept all my original ideas, numbers, and reasons.

**Moment 2**

- *What I asked for:* I asked the AI to read my label definitions (`analysis`, `hot_take`, `reaction`) and tell me if they are easy to understand.
- *What came back:* It suggested making a few sentences shorter and more direct.
- *What I changed:* I used a couple of its wording suggestions, but I made sure my core rules and boundaries stayed exactly the way I wrote them.

**Pre-labelling disclosure:**

Almost all of the work was done by me. I created the taxonomy, set the rules, and manually cold-labelled the first 20 comments. To save time typing and searching, I used an AI tool just to fetch the remaining 180 comments and give them a quick pre-label. However, I personally read and checked every single comment myself to make sure it matched my own rules before adding it to the file.

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy |  |  |  |
| Macro F1 |  |  |  |
| F1 — `label_one` |  |  |  |
| F1 — `label_two` |  |  |  |

**What I predicted before I looked:**
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->

**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->



---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

| true \ predicted |  |  |  |
|---|---|---|---|
| **** |  |  |  |
| **** |  |  |  |
| **** |  |  |  |

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->



---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 |  |  |  |  |
| 2 |  |  |  |  |
| 3 |  |  |  |  |
| 4 |  |  |  |  |
| 5 |  |  |  |  |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->



---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** ___ / 30 = ___%

<!-- Nobody grades this number. A 60% who argues every disagreement from the
     stated rules beats a 95% who wrote "staff was right" nine times. Several
     of the 30 were chosen because they're genuinely ambiguous — you should be
     winning some of these. -->

**Disagreements**

<!-- Three lines each: the post, both labels, and who you think is right and
     why — grounded in the staff definitions you were both applying.

     Then sort each into one of three piles:
       (a) the rule covered it and I applied it loosely → a consistency problem
       (b) the rule genuinely doesn't say               → a gap in the definitions
       (c) the rule is ambiguous here and my reading is defensible → argue it.
           This is a legitimate win.

     Pile (a) is the one that matters most for your diagnosis: if you applied a
     written rule two different ways on 30 posts, that is direct evidence about
     what you did across your own 200. -->

**1.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**2.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**What the pattern in my disagreements tells me:**



---

## The Improvement

**What I changed:**

**Which diagnosis pointed at it:**

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

**Did it help, and how do I know:**

<!-- If it didn't, say so. Relabelling that didn't help is a genuinely
     interesting result and earns full credit. -->



---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped. -->



**The gap between what I meant my labels to capture and what the model
learned:**
<!-- Two sentences. Your confusion matrix is the evidence. -->



<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 5

       [ ] criteria.md has five numbered criteria, each naming a NUMBER
       [ ] Each has a reason underneath tied to your data or taxonomy
       [ ] labels.csv: at least 150 rows, text/label/note, ONE file not split
       [ ] No label above 70%
       [ ] All five unit 5 sections have real content
       [ ] Label Taxonomy includes the decision rule for your hardest boundary
       [ ] The Dataset includes three hard cases
       [ ] results.json and test_split.csv committed (the notebook does this)
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN

     SUBMISSION CHECKLIST — unit 6

       [ ] Baseline vs. Trained table, with your prediction written beforehand
       [ ] Run Log — Before, five criteria across three seeds
       [ ] Confusion matrix TYPED AS A MARKDOWN TABLE
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, using the agreement report as evidence
       [ ] Agreement Report with every disagreement adjudicated
       [ ] One improvement, with Run Log — After
       [ ] What's Still Broken
       [ ] results_three_seeds_before.json, results_three_seeds_after.json,
           baseline_results.json and
           agreement_results.json committed
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
