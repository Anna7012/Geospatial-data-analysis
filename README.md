# Geospatial Analysis for Business Expansion

Identify **high-demand, low-presence** regions and recommend where to open new locations. The project visualises sales geographically (static maps + an interactive Leaflet map) and ranks candidate sites.

![Maps](outputs/expansion_maps.png)

## How it works

| Step | Method |
|------|--------|
| 1. Demand surface | Sales aggregated into a lat/lon grid (default 0.05° ≈ 5.5 km), Gaussian-smoothed to model catchment areas |
| 2. Presence surface | Haversine distance from every cell to the nearest store, plus stores within the catchment radius |
| 3. Opportunity score | `normalised demand × (1 − coverage)` |
| 4. Candidate sites | Top-scoring cells clustered with opportunity-weighted K-Means; cluster centroids = recommended sites |
| 5. Outputs | Ranked CSV, PNG maps, interactive HTML map (heatmap, stores, hot cells, ranked sites) |

## Quick start

```bash
pip install -r requirements.txt
python geospatial_expansion_analysis.py
```

With no arguments it runs on a **synthetic Kerala, India dataset** (30,000 sales, 14 stores concentrated in the south/central region, so the north surfaces as under-served). Open `outputs/expansion_map.html` in a browser (needs internet for map tiles).

### Use your own data

```bash
python geospatial_expansion_analysis.py --sales sales.csv --stores stores.csv
```

| File | Required columns |
|------|------------------|
| `sales.csv` | `lat`, `lon`, `amount` |
| `stores.csv` | `lat`, `lon` (optional: `store_id`, `name`) |

### Options

| Flag | Default | Meaning |
|------|---------|---------|
| `--cell-deg` | 0.05 | Grid cell size (degrees) |
| `--smooth` | 2.0 | Gaussian sigma in cells |
| `--catchment-km` | 15 | Store catchment radius |
| `--n-sites` | 8 | Number of recommended sites |
| `--top-pct` | 0.93 | Quantile of cells treated as "hot" |
| `--out` | outputs | Output folder |

## Outputs (`outputs/`)

- `expansion_map.html` – interactive map with toggleable layers
- `expansion_maps.png` – demand / coverage / opportunity panels
- `sales_density.png` – revenue hexbin map
- `recommended_sites.csv` – ranked sites with score, catchment revenue, distance to nearest store
- `grid_scores.csv` – full per-cell metrics

## Sample result (synthetic data)

Top sites: Kozhikode, Malappuram, Alappuzha-Kottayam corridor, Pathanamthitta, Kannur — large demand with the nearest store 50–170 km away.

## Notes & limitations

- The demo data is synthetic; replace it with real transactions for real decisions.
- Distance is straight-line (haversine), not travel time. Consider adding road-network isochrones (e.g. OSMnx) for production use.
- Competitor locations, rent, and demographics can be added as extra layers or score multipliers.

## License

MIT
