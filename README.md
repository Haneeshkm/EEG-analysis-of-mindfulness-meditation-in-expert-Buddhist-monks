# Temporal stability of the absolute EEG spectrum and non-monotonic relative-power redistribution during mindfulness meditation

Reproducible analysis code for the manuscript on within-session EEG dynamics during
a 30-minute mindfulness meditation in expert Thai Buddhist monks.

Every table and figure in the manuscript and Supplementary Information is regenerated
by the notebooks below from a single input file, `data/tidy_long.csv`.

---

## Key findings reproduced

- **Absolute spectrum is temporally stable** — no band shows a reliable monotonic
  trend across the session; median total power changes by only −6.5 %.
- **Relative power redistributes non-monotonically** — relative alpha follows a
  U-shaped course and relative theta and delta the mirror-image inverted-U.
- **The effect is compositional and robust** — alpha and theta curvature survive a
  centered-log-ratio (CLR) re-analysis and an isometric-log-ratio balance, replicate
  in both split halves, and are near-global across the scalp (Figure 4); delta does
  **not** survive the compositional test (it moves passively under the constant-sum
  constraint).
- **Two individual alpha phenotypes** — a declining majority (n = 33) and a rising
  minority (n = 22), unrelated to cohort.
- **Cross-sectional cohort decoding is a non-significant trend** — balanced accuracy
  0.45 vs 0.33 chance, permutation p = 0.06; because ordination cohort and age are
  collinear, no experience-specific signature can be established.

---

## Repository structure

```
.
├── README.md
├── requirements.txt
├── data/
│   ├── README.md            # data source (Mendeley DOI) and column dictionary
│   └── tidy_long.csv        # electrode-level band power (input to everything)
├── src/
│   └── prep.py              # shared loader: channel removal, ROI map, relative power, complete-case filter
├── notebooks/
│   ├── 00_data_preparation.ipynb          # audit + Supplementary Table S1
│   ├── 01_trajectory_and_compositional.ipynb  # Table 1, Table 2, broadband, effect sizes, CLR/ILR, LOO, split-half
│   ├── 02_electrode_topography.ipynb      # Figure 4 (per-electrode curvature maps)
│   ├── 03_phenotypes_and_decoding.ipynb   # Figure 3, clustering diagnostics, cohort decoding (Table S6)
│   ├── 04_interaction_tests.ipynb         # Supplementary Table S3 (time × cohort)
│   └── 05_article_figures.ipynb           # Figures 1 and 2 (trajectories by cohort)
└── outputs/                 # generated tables (.csv) and figures (.png/.pdf) land here
```

## Notebook → manuscript map

| Notebook | Reproduces |
|----------|------------|
| `00` | Supplementary Table S1; missing-data audit (60 → 57 → 55); closure check |
| `01` | Table 1; Table 2; §3.2 absolute stability + broadband; effect sizes; §3.4 CLR/ILR; leave-one-interval-out; split-half |
| `02` | Figure 4 (electrode-level curvature topographies) |
| `03` | Figure 3 (alpha phenotypes); Table S6 clustering diagnostics + cohort decoding |
| `04` | Supplementary Table S3 (time × cohort interactions) |
| `05` | Figures 1 and 2 (band-power trajectories by cohort) |

---

## Setup

Requires Python 3.9+.

```bash
git clone <this-repo-url>
cd <repo>
python -m venv .venv && source .venv/bin/activate      # optional
pip install -r requirements.txt
```

`mne` is optional; the topography notebook falls back to a self-contained renderer
if it is not installed.

## Running

The notebooks are independent (each re-loads the data through `src/prep.py`) but are
numbered in a natural reading order. Run all of them with:

```bash
cd notebooks
jupyter nbconvert --to notebook --execute --inplace *.ipynb
```

or open them individually in Jupyter. Generated tables and figures are written to
`outputs/`.

---

## Method notes

- **Participant chain:** 60 recorded → exclude E1-5, E1-6, E1-7 (no theta records)
  → **57** for relative-power analyses → exclude E1-8 and E3-3 (incomplete alpha
  trajectory) → **55** for phenotype clustering. Absolute-power analyses use all 60
  via mixed models with all available observations.
- **Relative power** is reconstructed as `100 × band / Σ(5 bands)` per electrode,
  so closure is exact.
- **Models:** linear mixed-effects with by-participant random intercept and random
  slope of time; the quadratic term is a fixed effect; likelihood-ratio tests for
  curvature and interactions; Benjamini–Hochberg FDR within each 25-test family.
- **Compositional analysis:** CLR (natural log) across the five bands; ILR balance
  contrasting alpha against the geometric mean of theta and delta.
- **Decoding:** random forest on 30 CLR features (25 band-by-region means + 5
  whole-scalp OLS slopes), subject-level stratified 5-fold CV, permutation test.
  Ordination cohort and age are collinear, so features are not age-residualized.

## Data availability

Input data: Wongupparaj P (2024), Mendeley Data,
https://doi.org/10.17632/pthdhf2dwm.1 (see `data/README.md`).

## Citation

If you use this code, please cite the manuscript (details to be added on
publication) and the original dataset above.

## License

Code released under the MIT License (add a `LICENSE` file). The dataset retains its
original Creative Commons license from the Mendeley deposit.
