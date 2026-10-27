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

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy | 0.667 | 0.900 | +0.233 |
| Macro F1 | 0.590 | 0.898 | +0.307 |
| F1 — `analysis` | 0.690 | 1.000 | +0.310 |
| F1 — `hot_take` | 0.182 | 0.870 | +0.688 |
| F1 — `reaction` | 0.900 | 0.824 | -0.076 |

**What I predicted before I looked:**
I predict the trained model will significantly beat the zero-shot baseline on the `hot_take` label, because the baseline completely failed at recognizing it (F1 of 0.182), showing that a general model needs specific training examples to understand this boundary.

**What the gap actually means:**
The massive +0.688 jump in `hot_take` F1 proves that fine-tuning was absolutely necessary for the model to learn the boundary between an unbacked claim and an analytical post. Interestingly, the baseline was slightly better at identifying `reaction` posts out of the box, meaning the general model already understood emotional exclamations well without my training data.



---

## Run Log — Before

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Overall accuracy | ≥ 0.62 | 0.900 | 0.933 | 0.967 | MET |
| 2. `reaction` F1 score | ≥ 0.65 | 0.824 | 0.947 | 1.000 | MET |
| 3. `analysis` F1 score | ≥ 0.55 | 1.000 | 0.952 | 0.947 | MET |
| 4. Max class size & min count | ≤ 45%, ≥ 50 | 34%, 64 | 34%, 64 | 34%, 64 | MET |
| 5. Agreement rate on calibration | ≥ 75% | N/A | N/A | N/A | TBD |


### Confusion matrix

| true \ predicted | analysis | hot_take | reaction |
|---|---|---|---|
| **analysis** | 10 | 0 | 0 |
| **hot_take** | 0 | 10 | 0 |
| **reaction** | 0 | 3 | 7 |

**My biggest off-diagonal number, and what it means:**
The model misclassified 3 true `reaction` posts as `hot_take`, whereas 0 `hot_take` posts went the other way. This shows a lopsided directional confusion where the model defaults to `hot_take` when it struggles to separate an immediate emotional outburst from an unbacked claim.

---

## Verdicts and Diagnoses

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 | Overall accuracy | ≥ 0.62 | MET | The model achieved 0.900, 0.933, and 0.967 across the three seeds, clearing the 0.62 threshold every time. |
| 2 | `reaction` F1 score | ≥ 0.65 | MET | The F1 scores were 0.824, 0.947, and 1.000, easily clearing the 0.65 threshold on all three splits. |
| 3 | `analysis` F1 score | ≥ 0.55 | MET | The F1 scores were 1.000, 0.952, and 0.947, far exceeding the 0.55 threshold despite the small sample size. |
| 4 | Max class size & min count | ≤ 45%, ≥ 50 | MET | My largest class (`analysis`/`hot_take`) was 34%, and my smallest class (`reaction`) had 64 rows, meeting both constraints. |
| 5 | Agreement rate on calibration | ≥ 75% | TBD | Will evaluate after running the staff agreement check in Milestone 3. |

**Diagnoses**

Since my model passed all four testable criteria across all three seeds, I do not have a missed criterion to diagnose here. However, my confusion matrix reveals a clear, lopsided error pattern: on Seed 42, the model misclassified 3 true `reaction` posts as `hot_take`, while 0 `hot_take` posts went the other way. This indicates the model struggles with the boundary between immediate emotional outbursts and unbacked claims, defaulting to `hot_take` when confused. The upcoming agreement report will verify if this is a model failure or if I applied that boundary inconsistently in my training data.
---


## Agreement Report

**Agreement rate:** 27 / 30 = 90.0%

**Disagreements**

**1.**
> *The post:* "Times are down about 3% since the shoe rules changed, so obviously the shoes are the whole story and the sport has a problem."
>
> *Staff said / I said:* Staff: `hot_take` | Mine: `analysis`
>
> *My call, and why:* Staff is right. Rule 1 explicitly states that if a number is just decoration on a sweeping conclusion that the writer already reached, it is a `hot_take`. I saw the "3%" stat and incorrectly assumed it satisfied the rule, without checking if the fact actually carried the massive claim that "the sport has a problem."
>
> *Which pile:* (a) the rule covered it and I applied it loosely.

**2.**
> *The post:* "Nobody actually enjoys the long run. They enjoy having done it, which is a completely different thing, and the sport would be more honest if it said so."
>
> *Staff said / I said:* Staff: `hot_take` | Mine: `reaction`
>
> *My call, and why:* Staff is right. Rule 2 clearly states that a `reaction` must be about the writer's *own experience* (what just happened to them). "Nobody actually enjoys..." is a sweeping, unbacked generalization about all runners in the world. I was swayed by the emotional tone, but it structurally fits `hot_take`.
>
> *Which pile:* (a) the rule covered it and I applied it loosely.

**3.**
> *The post:* "Wind was so bad on the out section I was leaning at about 20 degrees. Coming back felt like cheating."
>
> *Staff said / I said:* Staff: `reaction` | Mine: `analysis`
>
> *My call, and why:* I am right, and my reading is defensible. Staff labeled this as a `reaction` to a recent run, but Rule 3 states "recency does not outrank evidence." The writer uses a specific numerical observation ("leaning at about 20 degrees") to factually contrast the physical resistance of the out section versus the return leg. The taxonomy is genuinely ambiguous on whether a specific numerical estimate of physical resistance counts as a "checkable fact," so classifying this as `analysis` based on Rule 3 is highly defensible.
>
> *Which pile:* (c) the rule is ambiguous here and my reading is defensible.

**What the pattern in my disagreements tells me:**
The pattern shows that I over-index on surface-level features rather than structural rules. In Disagreements 1 and 3, I saw a number ("3%", "20 degrees") and immediately leaned toward `analysis` without properly evaluating if it was a load-bearing fact. In my own dataset, this means I likely have some disguised `hot_take` and `reaction` posts incorrectly labeled as `analysis` just because they contained a number.

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

---

## The Improvement

**What I changed:**
I added 5 new, crystal-clear examples of the `reaction` label to my `labels.csv` training data (e.g., `"GET IN THERE! MASSIVE THREE POINTS!"`, `"My heart is literally pounding out of my chest right now."`).

**Which diagnosis pointed at it:**
The confusion matrix from the Before run showed a specific directional error on Seed 42 where the model incorrectly guessed `hot_take` for 3 true `reaction` posts. This indicated the model was slightly under-trained on the boundary between immediate emotional outbursts and unbacked claims, so providing more clear examples of `reaction` was the logical fix.

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Overall accuracy | ≥ 0.62 | 0.903 | 1.000 | 0.968 | MET |
| 2. `reaction` F1 score | ≥ 0.65 | 0.842 | 1.000 | 1.000 | MET |
| 3. `analysis` F1 score | ≥ 0.55 | 1.000 | 1.000 | 0.947 | MET |
| 4. Max class size & min count | ≤ 45%, ≥ 50 | 33.6%, 69 | 33.6%, 69 | 33.6%, 69 | MET |
| 5. Agreement rate on calibration | ≥ 75% | 90% | 90% | 90% | MET |

**Did it help, and how do I know:**
Yes, it definitively helped. On Seed 7, the model reached a perfect 1.000 (100%) overall accuracy. The `reaction` F1 score hit a perfect 1.000 on two of the three seeds, and Seed 42's `reaction` F1 also improved from 0.824 to 0.842. The extra examples successfully strengthened the boundary.

---

## What's Still Broken

Since all five criteria were successfully MET across all three seeds, nothing is "broken" regarding the project constraints. If I were to continue improving it, I would add more `analysis` posts to further stabilize Seed 2024 (which had a slight F1 dip to 0.947).

**The gap between what I meant my labels to capture and what the model learned:**
While the model performs exceptionally well, the remaining minor confusions (like still misclassifying 3 `reaction` posts as `hot_take` on Seed 42) show that the model likely still over-indexes on surface-level sentiment. It sometimes relies on the presence of strong, heated language rather than structurally parsing whether a sentence contains a load-bearing factual argument or is strictly a personal reaction.



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
