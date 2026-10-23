# Mexico Real Estate Analysis

Are property prices in Mexico driven more by **property size** or by **location**?

This project cleans three inconsistent exports of Mexican property listings, combines them into one tidy dataset, and sets up the exploration of how price relates to size and location.

## Data

Three raw CSV files of property listings (700 rows each) in `data/raw/`.
Source:

| File | Price column | Location columns |
|---|---|---|
| `mexico-real-estate-1.csv` | `price_usd` (text, e.g. `"$67,965.56"`) | `state`, `lat`, `lon` |
| `mexico-real-estate-2.csv` | `price_mxn` (pesos) | `state`, `lat`, `lon` |
| `mexico-real-estate-3.csv` | `price_usd` | `place_with_parent_names`, `lat-lon` (combined) |

## Approach

1. **Inspect** each file (shape, types, missing values) before changing anything.
2. **Clean** each file into the same six columns and types:
   - File 1: dropped 117 rows with missing coordinates; converted `price_usd` from text to numeric.
   - File 2: converted pesos to USD at a fixed 19 MXN/USD; dropped 129 rows with missing coordinates.
   - File 3: dropped 118 rows with missing coordinates; split `lat-lon` into numeric `lat` and `lon`; extracted `state` from the place path.
3. **Combine** the three files: 583 + 571 + 582 = **1,736 listings**, no missing values.
4. **Verify** the saved file by reading it back and comparing it with the in-memory data.

Every cleaning decision is recorded in the cleaning log inside the notebook.

## Results so far

- 1,736 listings across 30 states.
- Median price about $99k (mean about $115k, so a minority of expensive listings pulls the average up).
- Median size 156 m² (range 60–385 m²).
- Listings are concentrated in a few states; several states have fewer than 10 listings, so state-level comparisons for them are not reliable.

## Limitations

- File 2 prices use a single fixed exchange rate, so those dollar values are approximations.
- Rows with missing coordinates were dropped (about 17–18% of each file).

## Repository structure

```
├── data/
│   ├── raw/          # original CSVs, never edited
│   └── processed/    # cleaned, combined dataset
├── notebooks/
│   └── 01_getting_to_know_the_data.ipynb
├── pyproject.toml    # project dependencies
└── uv.lock           # exact package versions
```

## How to reproduce

1. Clone the repo and install [uv](https://docs.astral.sh/uv/).
2. Run `uv sync` to create the environment (Python 3.14, pandas 3.0).
3. Open `notebooks/01_getting_to_know_the_data.ipynb`, select the `.venv` kernel and choose **Run All**.

Note: pandas 3.0 labels text columns `str`; older versions show `object`.