# Madrid Air Quality Dataset (IoT Sensor Network)

**Three weeks of station-level air-quality readings from 15 IoT sensor stations
across the Madrid metropolitan area:** 30,627 synchronized readings,
2024-11-25 → 2024-12-17, covering six pollutants plus temperature, dewpoint,
humidity and station GPS coordinates — a compact spatio-temporal dataset for
geo-spatial ML, time-series and air-quality analysis.

Kaggle mirror: [madrid-air-quality-dataset](https://www.kaggle.com/datasets/zhenruiweng/madrid-air-quality-dataset)
(Kaggle **Datasets bronze medal**).

## Quick facts

| | |
|---|---|
| Rows | **30,627** readings |
| Stations | **15** (IDs in `topic`: 529, 3227–3232, 4157, 5725, 8250–8255) |
| Period | 2024-11-25 → 2024-12-17 (~3 weeks) |
| Readings per station | 2,039–2,043 (balanced; stations report near-simultaneously) |
| Columns | **13** |
| Spatial coverage | lat 40.167–40.634°N, lon −4.013 – −3.277°W (Madrid metro) |
| Duplicates | 0 (station × timestamp is unique) |
| License | Apache-2.0 (same as the Kaggle page) |

## Column dictionary

The `topic` / `object_*` naming indicates the rows were decoded from
MQTT-published IoT sensor payloads (one JSON object per uplink); the
`object_` prefix marks fields unpacked from each message.

| Column | Description |
|---|---|
| `timestamp` | Reading time (ms-precision; near-zero median gap between stations) |
| `topic` | Station ID (MQTT topic number) |
| `object_pm2_5` | PM2.5 concentration (sensor reporting units) |
| `object_pm10` | PM10 concentration |
| `object_no2` | Nitrogen dioxide |
| `object_co` | Carbon monoxide |
| `object_so2` | Sulfur dioxide |
| `object_o3` | Ozone |
| `object_temp` | Temperature (°C, range −4 to 19) |
| `object_lat` / `object_lon` | Station GPS coordinates |
| `object_dew` | Dewpoint (°C) |
| `object_hum` | Relative humidity (%, range 30–101) |

Concentrations are in the sensor network's native reporting units; they were
**not** cross-verified against official reference stations. Typical medians:
PM2.5 = 8, PM10 = 16, NO2 = 14, SO2 = 1, O3 = 13, CO = 77.

## Data quality notes (read before modeling)

- **`object_co` contains sensor glitches**: min −279, max 37,828 against a
  99th percentile of 259 and a median of 77. Filter or winsorize before use —
  a quantile clip at ~p99 preserves the signal.
- **Missingness is mild and column-specific**: `object_dew` 896 nulls (2.9%),
  `object_so2` 689 (2.3%), `object_temp` 223, `object_co` 23, `object_o3` 10,
  `object_pm10` 1. All other columns are complete.
- Gaps up to 272 minutes exist between reporting batches (median station-to-station
  offset within a batch ≈ 0); plan resampling accordingly for time-series work.
- Station coordinates are constant per station — treat this as a fixed
  15-node spatial sensor grid over ~50 km.

## Suggested use cases

- Spatio-temporal interpolation (kriging / Gaussian processes over 15 nodes)
- Urban air-quality anomaly and event detection
- Geospatial ML benchmarks with a small, fully interpretable station grid
- Low-cost sensor vs. reference-grade data quality studies (with the caveat above)

## Files

```
├── data/madrid_air_quality.csv   # complete dataset (30,627 rows × 13 columns)
├── README.md
└── LICENSE                       # Apache-2.0
```

## Citation / acknowledgment

- Kaggle mirror: [zhenruiweng/madrid-air-quality-dataset](https://www.kaggle.com/datasets/zhenruiweng/madrid-air-quality-dataset).
- Column semantics in this README are derived from the data itself (MQTT/IoT
  payload structure); the upstream sensor network is credited on the Kaggle page.

## License

[Apache-2.0](LICENSE) — identical to the license declared on the Kaggle dataset page.
