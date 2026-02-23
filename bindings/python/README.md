# wareki-python

A fast, safe, and precise library for converting between the Gregorian Calendar and the Japanese Era (Wareki) calendar.
Written in Rust, powered by `PyO3` and `maturin`.

It strictly handles leap years and era boundaries (e.g., the transition from Heisei to Reiwa on May 1st, 2019).

## Features

- **High Performance**: Core logic is implemented in Rust.
- **Precision**: Relies on `chrono` for precise date handling, including leap years (e.g., Reiwa 6 / 2024 is a leap year).
- **Multiple Input Formats**: The `from_wareki` function accepts full era names (e.g. `令和`), single Kanji (e.g. `令`), and Romaji abbreviations (e.g. `R` or `r`).

## Supported Eras

- `令和` (Reiwa)
- `平成` (Heisei)
- `昭和` (Showa)
- `大正` (Taisho)
- `明治` (Meiji)

## Usage

```python
import wareki
import datetime

# Gregorian to Wareki
w = wareki.to_wareki(2026, 2, 23)
print(f"{w.era_name}{w.year}年") # "令和8年"

# Wareki to Gregorian
dt = wareki.from_wareki("令和", 8, 2, 23)
print(dt.isoformat()) # "2026-02-23"

# Short names
dt2 = wareki.from_wareki("R", 8, 2, 23)
```

## Repository
For more details and bindings for other languages (Node.js, Go), see the [GitHub Repository](https://github.com/namineco144/wareki-rs).
