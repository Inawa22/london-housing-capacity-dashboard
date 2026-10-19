# London Housing Capacity — Streamlit Prototype v3

This project contains the first **two objectives** of the London housing-capacity tool:

1. **Current housing capacity:** the number of *existing dwellings* by London local authority, based on MHCLG Live Table 100 (2012–2025). Includes headline totals, stock-by-sector composition, historical trends, borough rankings and map.
2. **Cost of housing:** buying prices and monthly private rents by borough, based on the two Excel workbooks in your Google Drive folder `Housing capacity tool data / Cost of housing`. Includes cards, borough rankings, trends, rent by number of bedrooms, optional rental property-type comparisons and maps.

## Run locally

```bash
cd london_housing_dashboard
python -m pip install -r requirements.txt
streamlit run app.py
```

Switch between the two objectives using **Dashboard section** in the left sidebar. Objective 2 opens on **Renting**, with a separate **Buying** tab. Change the geography, trend period, and reference month using the controls.

**Browser-only preview:** Open `cost_dashboard_preview.html` directly. It is a self-contained interactive example using the same real monthly data, with Buying/Renting, selected borough, summary cards, ranking and trends. It is **not the Streamlit server**; geographical polygons are not embedded in this offline preview.

## What's included

| File | Purpose |
|---|---|
| `app.py` | Combined app/router plus Objective 1 implementation. |
| `cost_dashboard.py` | Objective 2 Streamlit views and data checks. |
| `data/london_dwelling_stock_2012_2025.csv` | Baseline stock figures (from Live Table 100). |
| `data/LiveTable100.ods` | Original dwelling-stock workbook. |
| `data/house_prices.csv` | Curated monthly UK HPI (33 boroughs + separate London series). |
| `data/private_rents.csv` | Curated monthly ONS PIPR (32 boroughs + separate London series). |
| `boundaries.py` | Loads, validates and caches the 33 official GLA borough polygons; also supports user-supplied GeoJSON. |
| `tests/test_costs.py`, `tests/test_boundaries.py` | Data validation and map tests. |
| `scripts/extract_from_workbooks.py` | Optional one-time reproducible extraction of source .xlsx files. |
| `cost_dashboard_preview.html` | Browser-only interactive cost dashboard preview. |

## Key figures in the supplied snapshots

**August 2026 private rents:** London published regional average **£2,332/month** (not a simple average of boroughs). Highest-price borough: **Kensington and Chelsea (£3,690/month)**; lowest: **Bexley (£1,537/month)**. **City of London has no separate rental series** and is shown as unavailable, not £0.

**July 2026 purchase prices:** London published regional average **£550,037**. Highest-price borough: **Kensington and Chelsea (£1,232,640)**; lowest: **Barking and Dagenham (£375,604)**.

These refer to the respective latest months *within the supplied workbooks*; they are not guaranteed to be the latest data published today.

## Methodology and caveats

- **Geographic match:** ONS GSS local-authority codes, not text labels. Housing stock contains 33 local authorities; rents contain 32 plus London; house prices contain all 33 plus London.
- **Regional benchmarking:** London-wide prices and rents are **directly sourced regional figures**, not unweighted borough averages. Do not sum house prices or rents across boroughs.
- **What "average" means:** UK HPI modelled average prices are not the arithmetic mean of that month's transactions. At local-authority level the published series is smoothed using a three-month moving average. Latest HPI months may be revised.
- **Rent series:** Estimated average monthly private rents rounded to £1. PIPR is not seasonally adjusted. Annual rent changes use the published annual-change column, not growth derived from rounded prices.
- **Homeownership and rent are not affordability:** No income, financing, utilities, mortgage rates, taxes or housing benefits are modelled. Do not interpret the descriptive data as a causal effect of housing supply.
- **Historical coverage:** HPI January 1995–July 2026; rent January 2015–August 2026; current stock 2012–2025 (last four stock years provisional, see source notes).
- **Rent availability:** the City of London has **no borough-level rent series** in this source. It appears as grey (unavailable) on the Streamlit rent map when boundary geometry is available. City of London still appears in Buying and Objective 1.
- **Maps:** The Streamlit app attempts the official GLA borough boundary web service and caches the GeoJSON locally in `data/london_boroughs.geojson`; internet access is required on first use if no boundary file is already present. If the service cannot be reached, figures/charts continue working and a file-upload fallback appears. All 33 polygons must pass GSS-code validation.

## Original data and provenance

The source Excel workbooks are stored in the user's Google Drive folder **Housing capacity tool data / Cost of housing**:

- `UK House price index.xlsx` (five tabs: Metadata, By type, Average price, Index Price, Sales Volume). This prototype uses **Average price** for monthly borough/region figures.
- `priceindexofprivaterentsukmonthlypricestatistics.xlsx` (four tabs: Cover sheet, Contents, Notes, Table 1). This prototype uses **Table 1** with published overall rent, annual inflation, bedroom and property-type fields.

Original workbooks are **not bundled** in this ZIP to avoid duplicating an 18 MB spreadsheet; the curated CSVs are bundled. If you want to reproduce the extraction, place both original Excel workbooks in `data/raw/`, then run:

```bash
python scripts/extract_from_workbooks.py
```

Sources:
- MHCLG Dwelling stock Table 100: https://www.gov.uk/government/statistical-data-sets/live-tables-on-dwelling-stock-including-vacants
- UK House Price Index: https://www.gov.uk/government/collections/uk-house-price-index-reports
- ONS Price Index of Private Rents: https://www.ons.gov.uk/economy/inflationandpriceindices/datasets/priceindexofprivaterentsukmonthlypricestatistics
- London Borough boundaries (GLA, OGL v3): https://data.london.gov.uk/dataset/london-boroughs-e55pw

## Testing

Run:

```bash
python -m pytest -q tests/test_costs.py tests/test_boundaries.py
```

The code/data tests can run in CI independently of the Streamlit server. The Streamlit application itself must be launched in an environment with Streamlit installed for a final live-app integration test.

---

## Objective 3 — Unmet housing demand (v4)

Select **03 · Unmet housing demand** in the Streamlit sidebar. Three views are provided:

1. **Homelessness & temporary accommodation:** official MHCLG quarterly household assessments, prevention/relief, point-in-time TA household and child counts, TA rate per 1,000 households, ranked borough comparisons, reasons, trends, and map when GLA polygon data are available.
2. **Rough sleeping:** CHAIN quarterly people-seen numbers and borough/time comparisons. The unit is **people**, not households.
3. **Wider housing need:** observed London-wide overcrowding trend (English Housing Survey, source GLA *Housing in London 2025*). Borough waiting-list and Census overcrowding panels appear only after valid additional sources have been supplied; they are intentionally **not inferred**.

Run `python scripts/extract_homelessness.py` to regenerate the processed 2025/CHAIN datasets from the included original workbooks. Run `python scripts/extract_gla_overcrowding.py` to regenerate the London overcrowding context. See `OBJECTIVE3_SOURCES.md` for official links, coverage, known missing returns, revisions and optional CSV schemas.

### Source currency
The original 2025 statutory homelessness workbooks in the user's Drive were retained without alteration. GOV.UK's August 2026 release has since published revised Q3/Q4 2025 local-authority data and January–March 2026 figures. To obtain a fully current series, replace the original workbooks with the official revised versions and revalidate schema and extracted values. v4 is a first prototype using the files made available in the project folder.

If you have internet access locally, `python scripts/download_wider_need_sources.py` can fetch the official **raw** waiting-list and Census source files (subject to the publishers' APIs being available). Borough indicators remain pending until those files have been converted and validated against the optional schemas in `OBJECTIVE3_SOURCES.md`.
