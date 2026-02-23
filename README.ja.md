# wareki-rs
*[Read in English](README.md)*

Rust製の、西暦（グレゴリオ暦）と和暦（Japanese Era）を相互変換するための高速かつ安全なライブラリです。
Rustコアだけでなく、Python、Node.js、Go向けのネイティブバインディングも提供しています。

閏年や各元号の正確な境界（例：平成から令和への改元など）を厳密に処理します。

## 主な機能

- **高性能 & 安全**: コアロジックはRust (`cargo`) で実装されています。
- **正確な日付計算**: `chrono` クレートを利用することで、閏年（例：令和6年 / 2024年は閏年）なども正確に変換・検証します。
- **柔軟な入力形式**: 和暦から西暦への変換 (`from_wareki`) では、フルネーム (`令和`)、漢字1文字 (`令`)、ローマ字略称 (`R` または `r`) のいずれの形式も受け付けます。
- **各言語へのネイティブバインディング**:
  - Python (`PyO3` / `maturin` 経由)
  - Node.js (`napi-rs` 経由)
  - Go (`CGO` と Rustの C-FFI 経由)
- **統合された開発環境**: すべての言語のツールチェーンが含まれた DevContainer の設定を同梱しています。

## 対応している元号

- `令和` (Reiwa)
- `平成` (Heisei)
- `昭和` (Showa)
- `大正` (Taisho)
- `明治` (Meiji)

---

## 1. Rust (Core)

`Cargo.toml` に `wareki-core` を追加します。

```rust
use wareki_core::{to_wareki, from_wareki, Era};

fn main() {
    // 西暦から和暦への変換
    let w = to_wareki(2026, 2, 23).unwrap();
    println!("{}{}年", w.era_name(), w.year); // "令和8年"

    // 和暦から西暦への変換 (chrono::NaiveDate が返ります)
    let date = from_wareki("令和", 8, 2, 23).unwrap();
    println!("{}", date); // "2026-02-23"

    // 略称やローマ字の入力も可能です
    assert_eq!(from_wareki("R", 8, 2, 23).unwrap(), date);
}
```

---

## 2. Python (`wareki-python`)

`PyO3` でビルドされており、Python標準の `datetime.date` オブジェクトを返します。

### インストール (開発用)
```bash
cd bindings/python
python -m venv .venv
source .venv/bin/activate
pip install maturin
maturin develop
```

### 使い方
```python
import wareki

# 西暦 -> 和暦
w = wareki.to_wareki(2026, 2, 23)
print(f"{w.era_name}{w.year}年") # "令和8年"

# 和暦 -> 西暦
dt = wareki.from_wareki("令和", 8, 2, 23)
print(dt.isoformat()) # "2026-02-23"

# 略称もサポート
dt2 = wareki.from_wareki("R", 8, 2, 23)
```

---

## 3. Node.js (`wareki-node`)

`napi-rs` (`napi4`) により構築された高速なネイティブモジュールです。

### インストール
```bash
cd bindings/node
npm install
npm run build
```

### 使い方
```javascript
const wareki = require('./bindings/node/index.js');

// 西暦 -> 和暦
const w = wareki.toWareki(2026, 2, 23);
console.log(`${w.eraName}${w.year}年`); // "令和8年"

// 和暦 -> 西暦 (ISO 8601形式の文字列が返ります)
const ds = wareki.fromWareki("令和", 8, 2, 23);
console.log(ds); // "2026-02-23"
```

---

## 4. Go (`wareki-go`)

CGO を用いて Rust で静的コンパイルされたバイナリをリンクします。

### インストール
最初に Rust 側でCライブラリをビルドする必要があります。
```bash
cargo build --release -p wareki-c
```

### 使い方
```go
package main

import (
	"fmt"
	"github.com/user/wareki-go"
)

func main() {
	// 西暦 -> 和暦
	w, err := wareki.ToWareki(2026, 2, 23)
	if err == nil {
		fmt.Printf("%s%d年\n", w.EraName, w.Year) // "令和8年"
	}

	// 和暦 -> 西暦 (time.Time が返ります)
	dt, err := wareki.FromWareki("令和", 8, 2, 23)
	if err == nil {
		fmt.Println(dt.Format("2006-01-02")) // "2026-02-23"
	}
}
```
*注意: Goプログラムを実行・ビルドする際は、`target/release` 内の `libwareki_c.a` を指定する `CGO_LDFLAGS` などの環境変数の設定が必要になる場合があります。*

---

## 開発とテスト

本プロジェクトは DevContainer を使用しています。VS Code（Dev Containers拡張機能付き）または GitHub Codespaces でこのリポジトリを開くだけで、`rustup`, `python 3.12`, `node 20`, `go 1.22` がすべてセットアップされた環境が手に入ります。

### GitHub Actions (CI) による自動テスト
プッシュおよびプルリクエスト作成時に、自動的にテストが並行実行されるよう設定されています（`.github/workflows/ci.yml` 参照）。

### DevContainer 内での手動テスト
```bash
# Rust コアのテスト
cargo test -p wareki-core

# Pythonのテスト
(cd bindings/python && maturin develop && pytest test_wareki.py)

# Node.jsのテスト
(cd bindings/node && npm run build && npm test)

# Goのテスト
cargo build --release -p wareki-c
export CGO_LDFLAGS="-L$(pwd)/target/release -lwareki_c"
export LD_LIBRARY_PATH="$(pwd)/target/release:$LD_LIBRARY_PATH"
(cd bindings/go && go test -v)
```
