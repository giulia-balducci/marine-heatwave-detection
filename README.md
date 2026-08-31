# Marine Heatwave Detection: The Mediterranean Sea

**Status: in progress.** Data acquisition, NetCDF-to-Parquet conversion, exploratory SQL analysis, and marine heatwave event detection complete. Visualization notebook in progress: animated slider validated on a test period (Jan-Mar 2016), full 2016-2026 scale-up and visual polish still pending.

## Overview

SQL/DuckDB analysis of Mediterranean sea surface temperature to detect marine heatwave events in the northwestern Mediterranean, following the percentile-threshold methodology from Hobday et al. (2016), the standard definition used across marine heatwave literature.

## Study area

Northwestern Mediterranean: Tuscan Archipelago, Ligurian Sea, and Gulf of Lion (longitude 2–14°E, latitude 40.5–44.5°N). This region is documented by the Mare Caldo monitoring project (Greenpeace and DISTAV, University of Genoa) as one of the areas with the strongest ecological impact from marine heatwaves in the Mediterranean, including recurring gorgonian mortality events. I am also a certified guide for the Tuscan Archipelago National Park, which sits at the centre of this bounding box.

## Data

Mediterranean Sea: High Resolution L4 Sea Surface Temperature Reprocessed (Copernicus Marine Service, SST_MED_SST_L4_REP_OBSERVATIONS_010_021, DOI 10.48670/moi-00173). Daily, satellite-derived, 0.05° resolution, 2016–2026.

## Methodology

Marine heatwave detection follows the Hobday et al. (2016) definition: an event is a period of at least 5 consecutive days where SST exceeds the 90th percentile threshold for that calendar day, calculated from a climatological baseline.

Note on scope: the standard definition uses a 30-year baseline. This project uses 2016–2026 (10-11 years depending on calendar day) to keep the dataset manageable for a portfolio project. This is a documented simplification, not an attempt to match published climatological studies.

## Key findings

Exploratory SQL analysis (`02_eda_sql.ipynb`) shows a clear seasonal cycle, offset from the solar calendar: the coldest month is February (13.7°C average), not January, and the warmest is August (25.6°C average), not June. This lag is consistent with ocean thermal inertia: the sea keeps losing heat into February from autumn cooling, and keeps absorbing heat past the summer solstice before peaking in August.

The sharpest month-to-month jump is May to June (+4.3°C). This was predicted before running the query, based on personal scuba diving experience at Elba Island, where shallow-water dives typically require a drysuit until mid-to-late May: consistent with the same late-spring warming pattern found in the data.

Within-month temperature range (max minus min, across the full grid and time series) is widest in December (15.28°C), not June-July as predicted, though June-July came close (14.87–14.99°C). December's wider range is attributed to geographic spread within the bounding box: the Gulf of Lion, exposed to the Mistral wind, can be substantially colder than the Tyrrhenian on the same day, and cold snaps from Mistral events likely compound against still-warm Tyrrhenian waters carrying summer thermal lag.

## Marine heatwave detection results

The Hobday et al. (2016) methodology, implemented from scratch in SQL, detected **253,145 events** across the bounding box (2016-2026): consecutive runs of 5+ days above the calendar-day 90th percentile threshold, per grid cell. Median event duration is 8 days, with a long right tail (mean 12.35 days, maximum 161 days).

Marine heatwaves are not exclusively a summer phenomenon. Both investigated events occurred outside summer: the 2016 case (46 days, January-February) and the longest overall (161 days, October 2024-March 2025). Both were individually verified against raw SST data and confirmed as genuine sustained anomalies, not artifacts of the detection logic, comparable in kind to real documented events like the 2014-2016 Northeast Pacific "Blob," which also spanned multiple seasons. This pattern is consistent with recent Mediterranean-wide monitoring: Greenpeace's 2025 "Mare Caldo" report (with DISTAV, University of Genoa) documented widespread marine heatwaves across Italian coastal waters starting as early as late winter or spring, not confined to summer months.

## Stack

Python for ETL (copernicusmarine CLI, xarray, NetCDF to Parquet conversion), SQL via DuckDB for analysis.

## Structure

```
data/raw/          raw NetCDF (not versioned)
data/processed/     Parquet files (not versioned)
notebooks/
  00_data_acquisition.ipynb
  01_netcdf_to_duckdb.ipynb
  02_eda_sql.ipynb
  03_mhw_detection.ipynb
  04_visualization.ipynb      (in progress)
```
