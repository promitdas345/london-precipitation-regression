# Dataset

The dataset is **not included in this repository**. Download it and place
`london_weather.csv` in the **repository root**, next to `COMP3400_MiniProject.ipynb` —
the notebook reads it as `pd.read_csv('london_weather.csv')` from its own directory.

## Where it came from

The copy used for this project was supplied with the COMP 3400 (Winter 2026) mini project.
The same file is published on Kaggle:

- **London Weather Data** — https://www.kaggle.com/datasets/emmanuelfwerr/london-weather-data

The underlying observations come from the **European Climate Assessment & Dataset
(ECA&D)**, https://www.ecad.eu/ — a blended daily series for the London (Heathrow) station.

The Kaggle dataset is described by third parties as released to the public domain (CC0),
but that has not been confirmed against the licence field on the Kaggle page itself.
**Check the licence on Kaggle before redistributing the file.** That uncertainty is why
the CSV is not committed here.

## What the file contains

15,341 rows × 10 columns, one row per day, covering **1979-01-01 to 2020-12-31** with no
missing days.

Columns, with the range of values actually present in the file and the number of missing
rows. `precipitation` is the target variable. The course materials do not document units,
so none are claimed here beyond what the values themselves show.

| Column | Range in file | Missing rows |
|---|---|---|
| `date` | 19790101 to 20201231 | 0 |
| `cloud_cover` | 0 to 9 | 19 |
| `sunshine` | 0 to 16 | 0 |
| `global_radiation` | 8 to 402 | 19 |
| `max_temp` | -6.2 to 37.9 | 6 |
| `mean_temp` | -7.6 to 29 | 36 |
| `min_temp` | -11.8 to 22.3 | 2 |
| `precipitation` | 0 to 61.8 | 6 |
| `pressure` | 95960 to 104820 | 4 |
| `snow_depth` | 0 to 22 | 1441 |

`snow_depth` is missing for 9.39% of rows; 98.99% of the values that are present are 0.
