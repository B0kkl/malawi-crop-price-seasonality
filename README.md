# Malawi Crop Price Seasonality

**When should a Malawian farmer sell?** An analysis of 20+ years of market prices to find the best (and worst) months to sell maize and other crops.

Many smallholders sell right after harvest, when prices are lowest. This project measures how much prices rise and fall through the year for each crop, so the timing of a sale can be a decision rather than a default.

## Key findings

| Crop | Best months | Worst months | Gain, best vs worst | Years tested | Pattern held |
|---|---|---|---|---|---|
| Maize | Jan, Feb, Mar | May, Jun, Jul | 38% | 18 | 17 of 18 |
| Groundnuts (shelled) | Jan, Feb, Mar | May, Jun, Jul | 21% | 5 | 5 of 5 |
| Cassava | Dec, Jan, Feb | Apr, May, Jun | 18% | 10 | 9 of 10 |
| Rice | Feb, Mar, Apr | Jul, Aug, Oct | 11% | 14 | 12 of 14 |
| Beans | Jan, Feb, May | Apr, Jun, Oct | 11% | 7 | 6 of 7 |
| Cowpeas | Mar, Apr, May | Aug, Sep, Nov | 31% | 1 | indicative only |
| Pigeon peas | Mar, Apr, May | Jul, Aug, Oct | 52% | 1 | indicative only |
| Sweet potatoes | Aug, Sep, Oct | Feb, Mar, Jul | 48% | 1 | indicative only |

- Prices peak in the lean season (roughly December to March) and bottom out around harvest (May to July).
- **Maize** has the strongest and most reliable pattern.
- **Groundnuts and cassava** are the best non-maize options.
- **Rice and beans** move much less, so storing them is less likely to pay off.
- Crops tested on only one year (cowpeas, pigeon peas, sweet potatoes) are hints, not conclusions.

Full results: [`outputs/best_months_to_sell.csv`](outputs/best_months_to_sell.csv). Sorghum and millet had too little continuous data to analyse.

## Method

1. **Clean** the WFP price data: drop the incomplete final month and the national-average pseudo-market, keep food crops priced per kg, and build a mapping to FAOSTAT crop names.
2. **Remove inflation.** Malawi's prices rise sharply year on year, which hides seasonal swings. For each crop and market, every monthly price is divided by the centred 12-month moving average around it, so what remains is the seasonal movement.
3. **Build a seasonal index.** Take the median across markets for each year and month, average across years, and rescale so each crop averages 1.0. An index of 1.15 means prices are typically 15% above the yearly average in that month.
4. **Test consistency.** For each year, check whether the three best months averaged higher than the three worst. Report how many years the pattern held.

Series with long data gaps are skipped rather than interpolated.

## Limitations

- WFP prices are **retail market prices**, not farm-gate prices. Farmers usually receive less.
- A seasonal gain is **not profit**. Storage costs, pest losses and waiting for payment must come out of it.
- Recent years are thin: 2022 is almost empty for most crops, and maize has no prices for 2023 to 2024. The results lean on the long-run pattern and cannot show whether it has shifted recently.
- Weather shocks and policy changes (for example, export bans) can override the usual pattern in any year.
- Soybeans, tomatoes, potatoes and onions have too little history to analyse.

## Project structure

```
malawi-crop-price-seasonality/
├── notebooks/
│   ├── 01_explore.ipynb       # data quality, coverage, which crops are usable
│   ├── 02_clean.ipynb         # cleaning, crop mapping, saves clean data + DuckDB
│   └── 03_seasonality.ipynb   # seasonal index, best months, consistency, regions
├── raw_data/                  # WFP prices and FAOSTAT production (original files)
├── clean_data/                # produced by 02_clean
└── outputs/                   # results table and heatmap
```

## How to run

```bash
python -m venv jupyter-env
jupyter-env\Scripts\activate          # Windows (use source jupyter-env/bin/activate on Mac/Linux)
python -m pip install jupyterlab pandas duckdb plotly
jupyter lab
```

Open the notebooks in `notebooks/` and run them **in order** (01, 02, 03). Each notebook finds the repo root automatically.

## Data sources

- **WFP Food Prices for Malawi**, via the [Humanitarian Data Exchange](https://data.humdata.org/dataset/wfp-food-prices-for-malawi) (CC BY-IGO).
- **FAOSTAT** crop production data, Food and Agriculture Organization of the United Nations.

## Tools

Python, pandas, DuckDB, Plotly, JupyterLab.

## Next steps

- Combine the seasonal results with FAOSTAT production data to rank crops by market size.
- Add rainfall data to explain unusual years.
- Build a Streamlit dashboard with a crop selector and a best-months-to-sell chart.
- Collect farm-gate prices to complement the retail data.
