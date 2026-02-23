# wareki-go

A Go wrapper for `wareki-rs`, providing fast, safe, and precise conversion between the Gregorian Calendar and the Japanese Era (Wareki) calendar.

## Prerequisites

Because this Go module uses CGO to bind to the Rust core library, you must have **Rust** installed on your system to build the static C library (`libwareki_c.a`) before compiling your Go project.

## Installation & Setup

1. Add the module to your Go project:
   ```bash
   go get github.com/namineco144/maturin-build/bindings/go
   ```

2. Build the Rust static library (you must have the `maturin-build` repository structure locally if building from source):
   ```bash
   cargo build --release -p wareki-c
   ```

3. Configure CGO environment variables to point to the built static library when running or building your Go code:
   ```bash
   export CGO_LDFLAGS="-L/path/to/maturin-build/target/release -lwareki_c"
   export LD_LIBRARY_PATH="/path/to/maturin-build/target/release:$LD_LIBRARY_PATH"
   
   go build .
   # or
   go run main.go
   ```

## Usage

```go
package main

import (
	"fmt"
	"github.com/namineco144/maturin-build/bindings/go"
)

func main() {
	// Gregorian to Wareki
	w, err := wareki.ToWareki(2026, 2, 23)
	if err == nil {
		fmt.Printf("%s%d年\n", w.EraName, w.Year) // "令和8年"
	}

	// Wareki to Gregorian (Returns time.Time)
	dt, err := wareki.FromWareki("令和", 8, 2, 23)
	if err == nil {
		fmt.Println(dt.Format("2006-01-02")) // "2026-02-23"
	}
}
```
