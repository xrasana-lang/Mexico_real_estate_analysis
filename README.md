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
Mexico_real_estate_analysis/
├── data/
│   ├── raw/          # original CSVs, never edited
│   └── processed/    # cleaned, combined dataset
├── notebooks/
│   ├── 01_getting_to_know_the_data.ipynb
│   ├── 02_visualising_housing_data.ipynb
│   ├── 03_correlation_and_variable_relationships.ipynb
│   └── 04_price_per_m2_and_location_effects.ipynb
├── pyproject.toml    # project dependencies
├── uv.lock           # exact package versions
└── README.md
```

## How to reproduce

1. Clone the repo and install [uv](https://docs.astral.sh/uv/).
2. Run `uv sync` to create the environment (Python 3.14, pandas 3.0).
3. Open `notebooks/01_getting_to_know_the_data.ipynb`, select the `.venv` kernel and choose **Run All**.

Note: pandas 3.0 labels text columns `str`; older versions show `object`.

## Visualisation findings

- Property prices and areas are right-skewed, with prices more strongly skewed.
- House and apartment median prices are similar, at approximately $98k–$99k.
- Price has a weak-to-moderate positive visual relationship with property area, but prices vary widely at similar sizes.
- Price levels and the apparent strength of the size–price relationship differ across Distrito Federal, Estado de México, and Yucatán.
- These are exploratory visual observations, not statistically tested conclusions. State-level comparisons are also affected by differences in listing counts.

## Correlation analysis findings

- Property area and price have a moderate positive Pearson correlation (\(r \approx 0.586\)) across the 1,736 listings.
- The squared correlation (\(r^2 \approx 0.343\)) indicates that the linear relationship accounts for about 34% of the observed variation in price in this dataset. This is an association, not evidence of causation.
- The area–price correlation differs by property type: approximately 0.519 for apartments and 0.719 for houses.
- Latitude and longitude have a correlation of approximately -0.480. This describes a geographic pattern in the coordinates and should not be interpreted as a direct effect of location on price.
- The price-per-square-metre scatterplot suggests a curved downward pattern as property area increases, indicating that the relationship may not be fully described by a linear correlation.
- State-level correlations vary, but results for states with very few listings can be unstable and should not be interpreted as reliable estimates.

These findings are exploratory and describe this dataset. They do not establish causal relationships or prove that the same patterns hold across the entire Mexican property market.