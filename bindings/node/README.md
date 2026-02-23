# wareki-node

A fast, safe, and precise library for converting between the Gregorian Calendar and the Japanese Era (Wareki) calendar.
Powered by Rust and `napi-rs`.

It strictly handles leap years and era boundaries (e.g., the transition from Heisei to Reiwa on May 1st, 2019).

## Installation

```bash
npm install wareki-node
```

*(Note: Currently in development. To build from source, clone the repository and run `npm run build`.)*

## Usage

```javascript
const wareki = require('wareki-node');

// Gregorian to Wareki
const w = wareki.toWareki(2026, 2, 23);
console.log(`${w.eraName}${w.year}年`); // "令和8年"

// Wareki to Gregorian (Returns ISO 8601 string)
const ds = wareki.fromWareki("令和", 8, 2, 23);
console.log(ds); // "2026-02-23"
```

## Supported Eras

- `令和` (Reiwa)
- `平成` (Heisei)
- `昭和` (Showa)
- `大正` (Taisho)
- `明治` (Meiji)

## Repository
For more details and bindings for other languages (Python, Go), see the [GitHub Repository](https://github.com/namineco144/wareki-rs).
