# Input datasets

- `LiveTable100.ods`: official UK government Dwelling Stock Table 100, from the source file supplied in the user's Drive (`Total current housing capacity` folder).
- `london_dwelling_stock_2012_2025.csv`: exact borough/year values extracted from the source ODS by `../prepare_data.py` for the 33 London local authority codes (`E090...`).

The CSV contains no fabricated observations and uses the source's official tenure/provider categories, **not** an inferred owner-occupied/private-rented breakdown. The housing stock includes vacant dwellings.

To replace `LiveTable100.ods` with a new official version, update `prepare_data.py`'s year range if necessary and rerun it.
