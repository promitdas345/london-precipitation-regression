# London Precipitation Regression

Predicting daily precipitation in London from other weather measurements with linear
regression, and testing whether three data preparation techniques — transformation,
feature selection, and outlier detection — improve the prediction.

Every preparation step is written by hand in NumPy and Pandas. scikit-learn is used only
for `LinearRegression`, `train_test_split`, and `r2_score`; none of its preprocessing
utilities are used, because the assignment did not allow them.

Coursework for COMP 3400 (Data Preparation Techniques), Memorial University of
Newfoundland, Winter 2026. Individual mini project.

## Dataset

Daily London weather observations, 15,341 rows × 10 columns, covering 1979-01-01 to
2020-12-31. The copy used here came with the course; the same dataset is published on
Kaggle as [London Weather Data](https://www.kaggle.com/datasets/emmanuelfwerr/london-weather-data),
with the underlying observations from the [European Climate Assessment & Dataset](https://www.ecad.eu/).

The CSV is not committed to this repository. See [`data/README.md`](data/README.md) for
the column list and download instructions.

## Cleaning

- **`snow_depth` missing values filled with 0** rather than dropped. 1,441 rows (9.39%)
  were missing it — too many to discard. The missing rows skew warm: mean temperature
  15.3 where `snow_depth` is missing against 11.1 where it is present, and 98.99%
  of the values that are present are already 0. Filling with 0 matches what the
  surrounding data implies.
- **`date` dropped**, as the assignment requires.
- **Remaining rows with any missing value dropped** — 80 rows, leaving 15,261.
- **Split 80/20** with `random_state=42`: 12,208 training rows, 3,053 test rows.

Every later stage is fitted on training data only and evaluated on the same untouched
test set, so the R² values below are comparable to each other.

## Method

**Transformation** — min-max scaling, z-score standardization, and decimal scaling, each
implemented directly, each computed from training statistics and applied to the test set.

**Feature selection** — Pearson correlation matrix on the training data (plotted as a
heatmap), then dropping features above a correlation threshold. The correlated pairs are
`mean_temp`/`min_temp` 0.96, `max_temp`/`mean_temp` 0.91, `max_temp`/`min_temp` 0.81, and
`sunshine`/`global_radiation` 0.85.

**Outlier detection** — column-wise z-scores, dropping a training row when at least *m*
columns exceed a threshold *T*. The test set is never filtered.

## Results

R² on the held-out test set, at the four decimal places used in the submitted report.

| Stage | R² |
|---|---|
| Baseline linear regression | 0.1515 |
| Transformation 1 — min-max scaling | 0.1515 |
| Transformation 2 — standardization | 0.1515 |
| Transformation 3 — decimal scaling | 0.1515 |
| Feature selection 1 — drop `min_temp`, `max_temp`, `sunshine` | 0.1480 |
| Feature selection 2 — drop `min_temp`, `max_temp` | 0.1489 |
| Feature selection 3 — drop `snow_depth` | 0.1515 |
| **Outlier detection 1 — T=3, m=2 (7 rows removed)** | **0.1516** |
| Outlier detection 2 — T=2.5, m=2 (116 rows removed) | 0.1513 |
| Outlier detection 3 — T=3, m=3 (0 rows removed) | 0.1516 |

The best result is outlier detection at T=3, m=2, which removed 7 training rows. The
notebook prints every value at full precision; at four decimals, feature selection 3
rounds to the same figure as the baseline although it is fractionally below it.

Two things worth being straight about:

- **The three transformations change nothing.** All three are linear rescalings, and
  ordinary least squares is invariant to them — each one reproduces the baseline R² to
  within floating-point rounding, a difference of about 6×10⁻¹⁶. That is the expected
  result, not a null finding.
- **The improvement is very small.** Rounded to four decimals it is 0.1515 → 0.1516; in
  full precision it is 0.1515261 → 0.1515705, a difference of 0.0000444. It is
  reproducible with this seed, but it is not evidence that outlier removal meaningfully
  helps this model.

## Limitations

- Linear regression accounts for roughly 15% of the variance in daily precipitation here.
  Daily rainfall is close to the hardest case for a linear model: the target is bounded
  below at zero, heavily skewed, and mostly driven by processes these nine columns do not
  record.
- Results come from a single 80/20 split with one seed. There is no cross-validation, so
  differences this small are within the range a different split could produce.
- Feature selection used a correlation threshold chosen by hand, and the outlier
  thresholds *T* and *m* were chosen by hand from a small number of attempts. Neither was
  tuned systematically.
- No feature engineering: no lagged variables, no seasonal terms, no interactions. The
  assignment scope was preparation techniques applied to the columns as given.
- The assignment permitted at most three attempts per stage, so each stage stops early by
  design rather than at a point of diminishing returns.

## Running it

```bash
pip install -r requirements.txt

# download london_weather.csv into this directory first — see data/README.md
jupyter notebook COMP3400_MiniProject.ipynb
```

Run all cells top to bottom. The notebook takes about 20 seconds and prints the R² for
every stage, ending with a summary cell.

Verified on Python 3.13.9 with numpy 2.3.5, pandas 2.3.3, scikit-learn 1.7.2, and
matplotlib 3.10.6 — the notebook runs top to bottom with no errors and reproduces the
table above.

## Repository contents

| Path | What it is |
|---|---|
| `COMP3400_MiniProject.ipynb` | The submitted notebook, unmodified |
| `COMP3400_MiniProject_Report.pdf` | The submitted report |
| `data/README.md` | Dataset source, columns, download instructions |
| `drafts/` | Earlier working copies, kept for history — see [`drafts/README.md`](drafts/README.md) |
