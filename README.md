# Project: Analyze A/B Test Results

## Project summary

This project analyzes a **69,889-user A/B test** run by an e-commerce company deciding whether to replace its old page (control) with a new one (treatment). The provided `ab_data.csv` has three columns — `country`, `group`, `converted` — and no missing values. `analyze_ab_test_results_project.ipynb` answers the full brief in order: **Part I** descriptive statistics (row count, conversion proportion, missingness, country counts, dtypes, unique values), **Part II** probability (marginal, conditional by group, conditional by country, and a country × group conversion table), and **Part III** a null-hypothesis simulation (single trial, 500 trials, histogram, simulated p-value, written interpretation). The notebook contains 59 cells (28 code, 31 markdown), all 28 code cells are executed in order, and a clean `Restart & Run All` pass re-executes every cell with **zero errors and zero warnings**.

Headline figures: overall conversion **13.05%** (9,119/69,889); assignment is 35,211 treatment (50.38%) vs. 34,678 control (49.62%); countries are US 48,850, UK 17,551, CA 3,488 (P(CA) **4.99%**). Control converts at **10.53%** (3,650) against treatment at **15.53%** (5,469) — a **+5.01 percentage-point** gap (0.0501). By country: US **13.28%**, UK **12.51%**, CA **12.53%**. The interaction table — control 10.7% / 10.2% / 9.4% versus treatment 15.8% / 14.9% / 15.4% for US / UK / CA — shows the treatment winning by 4.7–6.0 pp in every market with no reversal, i.e. no meaningful country × group interaction. Under the null both arms share the pooled rate **0.130478**; 500 simulated differences have mean **0.0000** and SD **0.0025**, while the observed difference sits ≈19.7 theoretical standard errors away, so the simulated **p-value is 0.0000** (not one of 500 null draws reached the observed gap). We therefore **reject H₀** and recommend adopting the new page. Every reported count and rate was re-derived from the raw CSV by a second implementation that uses only the standard library (`csv`, no pandas): **33 checks, 0 failures**. The 5-slide presentation already carries the same figures and conclusion.

## How to run

1. Install the dependencies (Python 3.14.7 verified):

   ```bash
   pip install pandas numpy matplotlib jupyter
   ```

2. Open and run the notebook, either interactively:

   ```bash
   jupyter notebook analyze_ab_test_results_project.ipynb
   ```

   …and use **Kernel → Restart & Run All**, or headlessly (the exact verification used here):

   ```bash
   python -m nbconvert --to notebook --execute --inplace --ExecutePreprocessor.timeout=300 analyze_ab_test_results_project.ipynb
   ```

   No network access is needed: the dataset is local. Random simulation results vary between runs because NumPy's RNG is not seeded, but the conclusion does not — see [Review findings](#review-findings-resolved-before-writing-this-readme).

3. Outputs are written into the notebook itself:
   - the bar chart of country counts (Part I e) and the histogram of simulated differences (Part III f)
   - all printed counts, rates, and the p-value in their respective cells

## Data sources

- **`ab_data.csv`** (local, 1,048,891 bytes): 69,889 rows × 3 columns — `country` (US/UK/CA), `group` (control/treatment), `converted` (0/1). No missing values in any column.
- Course dataset link (Udacity CDN): <https://video.udacity-data.com/topher/2023/February/63f67d64_ab_data/ab_data.csv>
- This workspace copy contains only the three analysis columns; it has no `user_id`, `timestamp`, or `landing_page`. That limits integrity checks (no duplicate-user detection, no group-vs-landing-page mismatch test) but does not affect any requested calculation.

## Key results (copy into the slide deck)

| Slide | Result |
| :--- | :--- |
| **Experiment Setup** | 69,889 users: 35,211 treatment (50.38%) and 34,678 control (49.62%). Countries: US 48,850 / UK 17,551 / CA 3,488. Overall conversion 13.05% (9,119). |
| **Conversion Rates** | Control 10.5% vs. treatment 15.5% (+5.0 pp). Country × group: control 10.7% / 10.2% / 9.4% and treatment 15.8% / 14.9% / 15.4% for US / UK / CA — a consistent 4.7–6.0 pp advantage, no interaction. |
| **Experiment Results** | Observed difference 0.0501; null distribution mean 0.0000, SD 0.0025 (500 draws); **p-value 0.0000**. Reject H₀ at α = 0.05: the treatment converts at a significantly higher rate, so implement the new page. |

## Rubric conformance

Reviewed against `docs/2. Rubric.md` and the task list in `docs/1. Project Details.md`:

| Rubric criterion | Status |
| :--- | :--- |
| **Functionality of code** — all cells run without error | ✅ 28/28 code cells executed in order; clean `nbconvert --execute` run, 0 error outputs and no warnings in any cell output |
| **Good coding practices** — docstrings, comments, readable names | ✅ Every non-trivial cell has an explanatory comment (what and why); descriptive names such as `p_treatment_rate`, `conversion_rates_by_group_country`, `p_diffs`, `obs_diff`; no single-letter variables |
| **Correct interpretation of results** — statistical *and* practical reasoning | ✅ Part II d) compares the rates; II i) compares countries; II j) discusses interaction; III h) explains the p-value, names the Type I error rate, and states the reject-H₀ / implement decision |
| **Correct values for all statistical results** | ✅ Independent standard-library re-derivation of every count/rate: **33/33 checks passed**; all table cells and identities (weighted rates reproduce the overall rate) verified |
| **Appropriate presentation slideshow** | ✅ 5-slide deck (`analyze_ab_test_results.pptx` / `.pdf`) carries the setup, the country × group table, the p-value, and the recommendation; figures match the notebook exactly |
| *Standout:* expanded EDA | ✅ Country × group breakdown plus cross-country comparison, beyond the minimum asked |
| *Standout:* logistic regression / propensity analysis | ⚠️ Not implemented — recorded as future work in `docs/3. Candidate Notes.md` §10 |

### Review findings (resolved before writing this README)

1. **The brief's expected narrative contradicts this dataset's result.** The task description anticipated `p_value > 0.05` → fail to reject H₀, but the data produce a +5.01 pp treatment advantage and a simulated p-value of 0.0000. The written answer in Part III h) reports the computed result (reject H₀, implement the new page) rather than the template narrative; the slide deck follows the data. This mismatch is documented explicitly rather than papered over.
2. **The simulation is not bit-reproducible.** The pre-filled first cell seeds Python's `random` module, not NumPy's RNG, and the 500 draws use `np.random.choice`. Re-running gives different `p_diffs`, but not a different conclusion: the observed gap is ≈19.7 theoretical standard errors from zero and the simulated p-value is 0 in every run. The pre-filled cell was deliberately left untouched.
3. **Plot ordering.** The histogram cell runs before `obs_diff` exists, so no reference line was added there; doing so would have raised a `NameError` on a clean run.
4. **Table placeholders.** The solution table in markdown cell 38 was filled with the computed UK/CA values; the question prompt in cell 35 was left exactly as provided, so the brief's "complete the table" exercise remains visible.

## Files

- `analyze_ab_test_results_project.ipynb` — the completed notebook (59 cells: 28 code, 31 markdown), outputs preserved, 91,577 bytes
- `ab_data.csv` — the dataset (69,889 × 3, 1,048,891 bytes, no missing values)
- `analyze_ab_test_results.pptx` — 5-slide presentation template filled with results (not modified by the analysis work)
- `analyze_ab_test_results.pdf` — PDF export of the deck, as required for submission
- `docs/` — `1. Project Details.md`, `2. Rubric.md`, `3. Candidate Notes.md`
- `LICENSE.md` — project license

## Submission

Both artifacts are graded: the notebook's code/results and the presentation's figures/interpretation (see `docs/2. Rubric.md`). Prepare a zip containing:

1. `analyze_ab_test_results_project.ipynb` — the executed notebook (all outputs present).
2. `analyze_ab_test_results.pdf` — the completed deck as PDF.

If you use the Google Slides template instead, submit the viewable link with link-sharing enabled for anyone with the link.
