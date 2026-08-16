# Codebook — Rural Health Statistics of India (harmonised, 2005–2023)

A datasheet for the harmonised RHS dataset: what it contains, how it was built,
what the values mean, and how to use it responsibly. This documentation is open
even while the dataset files are shared on request (see *Access*).

**Version 1.0 · 2026 · Association for Socially Applicable Research (ASAR)**

---

## 1. Overview

| | |
|---|---|
| Indicators | 863 canonical indicators |
| Domains | 12 (Demographics, Facilities, Workforce Rural, Workforce Urban, Infrastructure, Tribal, Coverage, Training, Five-Year Plan, FRU, HWC, Other) |
| Geography | 36 current States & Union Territories (40 including historical entities) |
| Time | 19 annual rounds, 2005 to 2022-23 |
| Observations | 2,31,597 (long format: one row per Year × State × Indicator) |
| Source | Rural Health Statistics annual reports, Ministry of Health & Family Welfare, Government of India |

The Rural Health Statistics (RHS) are the Government of India's annual account
of public-health infrastructure and workforce at the sub-national level —
Sub Centres (SCs), Primary Health Centres (PHCs), Community Health Centres
(CHCs), the health workforce staffing them, building status, and separate
tables for tribal areas. This project compiles 19 years of those reports into
one consistent, analysis-ready dataset.

## 2. Files

| File | Shape | Contents |
|---|---|---|
| `RHS_All_Years_Long.csv` | long | `Year, State, Category, Metric, Value` — the master table |
| `RHS_Combined_Data_Dictionary.csv` | reference | all 863 indicators: code, description, domain, year coverage, raw source names |
| `RHS_per_year_tables.zip` | wide | 19 CSVs, one per year, each State × Indicator as published |
| `RHS_thematic_tables.zip` | wide | 12 domain tables, State × (Indicator-Year) |

## 3. How to read an indicator code

Most codes follow the pattern **`[CADRE/FACILITY]_[AREA]_[STATUS]`**.

- **Area:** `_R_` rural · `_U_` urban · `_T_` tribal.
- **Status:** `_R` required (per IPHS norms) · `_S` sanctioned · `_P` in position ·
  `_V` vacant · `_SH` shortfall.
- **Facility counts:** `R_SC`/`P_SC`/`SH_SC` (required / in-position / shortfall
  Sub Centres); `F_SC_R` functional rural SCs (2019+ naming).
- **Building position:** `_GB` government building · `_RB` rented ·
  `_RFP` rent-free panchayat/voluntary · `_BRC` buildings required to be
  constructed · `_BUC` buildings under construction.

Examples: `DOC_PHC_R_P` = doctors in position at rural PHCs; `SC_T_R` = required
Sub Centres in tribal areas; `TSP_CHC_R_SH` = shortfall of total specialists at
rural CHCs. The data dictionary gives a plain-English description for every code.

## 4. Special values — never zeroed

Source markers are preserved exactly as reported and are **not** converted to
zero:

| Marker | Meaning |
|---|---|
| `*` | Surplus — in-position meets or exceeds requirement |
| `**` | Surplus (alternate marker used in some tables) |
| `NA` | Not available — the state did not report the figure |
| `N App` | Not applicable — the facility type does not apply to that state/UT |

In the long-format table these appear as the value or as an absent row (the
long table carries numeric values; a `*`/`NA`/`N App` cell is not emitted as a
numeric observation). A blank is meaningful: it is *not* a zero.

## 5. Two reporting eras (harmonisation)

The RHS report was redesigned in 2019, changing many column names and adding
Health & Wellness Centre (HWC) tables. This dataset maps **1,010 raw column
names → 863 canonical indicators** so a single indicator can be followed across
the break:

- **2005–2018 (Era 1):** e.g. `DOC_PHC_S`, `F_SC`.
- **2019–2023 (Era 2):** e.g. `DOC_PHC_R_S`, `F_SC_R`, plus HWC-integrated names.

**2019 caveat.** In the 2019 round, Sub Centre and PHC counts were published
under HWC-integrated column names (e.g. `SC_HWCSC_T_P`) reflecting the
integration of upgraded facilities. These are the same underlying facilities;
the dataset and the Explorer read those columns for 2019. Their values slot
smoothly between 2018 and 2020.

## 6. Requirement basis (IPHS)

"Required" figures follow the Indian Public Health Standards (IPHS) population
norms (broadly: one SC per ~3,000–5,000 people, one PHC per ~20,000–30,000, one
CHC per ~80,000–1,20,000, varying by rural/tribal/hilly area). Requirements are
recalculated as census population estimates are updated, so the required counts
step up at census transitions rather than drifting yearly. The 2005 report
prints two requirement tables (1991- and 2001-census basis); this dataset uses
the **2001-census** table for internal consistency with the following years.

## 7. Boundary and entity changes (tracked explicitly)

- **Andhra Pradesh / Telangana:** Telangana was formed from Andhra Pradesh in
  2014. Rows before 2015 are the **undivided** Andhra Pradesh.
- **Jammu & Kashmir / Ladakh:** reorganised in 2019-20. J&K figures before 2020
  include present-day Ladakh; Ladakh appears separately from 2020.
- **Dadra & Nagar Haveli and Daman & Diu:** the two UTs merged in 2020. This
  dataset presents them **combined across all years** (summed before the merger)
  so the entity is continuous.
- All 36 current States/UTs report across the series where the source does;
  historical entities bring the distinct-label count to 40.

## 8. Provenance and verification

Built PDF-first: the printed report is treated as ground truth.

1. **Source** — 19 annual RHS reports (2005 to 2022-23) as released in PDF;
   where a year circulated only as a scanned/image PDF (2012, 2022-23), the
   Ministry's own spreadsheet annexes were used.
2. **Extraction** — tables parsed per report; OCR artefacts corrected against
   the printed page.
3. **Harmonisation** — raw columns mapped to canonical indicators; state names
   and boundary changes reconciled.
4. **Verification** — 1,300+ automated checks including structural integrity,
   arithmetic identities, cross-year consistency, and **value-by-value
   comparison of headline facility figures against the source tables for all 19
   years**. Endpoint tables (2011, 2021) were verified 100%.

## 9. Changelog

**v1.0 (2026)** — first public release. Includes three source-corrections made
during verification and confirmed against the original reports:

- **2006** — three consecutive workforce tables (Pharmacists / Lab Technicians /
  Nursing staff) were rotated in an earlier extraction; re-extracted from the
  source PDF (e.g. nursing in-position corrected to 28,930).
- **2015** — every workforce table was shifted one column in an earlier
  extraction; re-extracted from the Ministry spreadsheets (e.g. doctors in
  position corrected to 27,421), and 75 previously-empty tribal workforce
  columns were filled.
- **2022-23** — OCR-damaged state-name cells had dropped several rows from the
  digital annexes; restored via verified alias mapping, including the full
  Nagaland column and a blank-name Chhattisgarh row.

## 10. Known limitations

- **Granularity** — state/UT level, not district. Sub-state variation is not
  captured.
- **Presence, not quality** — a facility counted as functional is not
  necessarily adequately staffed, equipped, or utilised.
- **Reporting gaps** — some state-years are reported "Not Applicable" or "Not
  Available" in the source; these are preserved as such, not imputed.
- **Boundary transitions** — reorganisations (Telangana, Ladakh) may create
  step changes in a state's series independent of real infrastructure change.
- **No imputation** — missing values are left missing throughout.

## 11. Access

The dataset files are shared on request while the collection is prepared for
formal release. Request access via the Explorer or by email
(surajbhor.lkml@gmail.com) with a line on intended use. This documentation, the
indicator dictionary, and the interactive Explorer are openly available.

## 12. License and citation

Licensed **CC BY-NC-ND 4.0** (Attribution-NonCommercial-NoDerivatives). See `LICENSE`.

> Bhor, S., & Zadey, S. (2026). *Rural Health Statistics of India — harmonised
> dataset, 2005–2023* (Version 1.0) [Data set]. Zenodo.
> https://doi.org/10.5281/zenodo.21966746

This is an independent research compilation, not an official publication of the
Ministry of Health & Family Welfare. For policy or academic use, cross-check
figures against the original report of the relevant year.

## 13. Acknowledgements

We thank Yash Jawale for valuable feedback on the release of the dashboard.
