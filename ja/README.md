# 輸出

## 概要

[Hello Worldの例](/example-redirect?exampleName=hello-world)では、JavascriptでWebAssemblyからエクスポートされた関数を呼び出しました。エクスポートとその使用方法についてもう少し詳しく見てみましょう。

---

## 実装

まだ行っていない場合は、 [Hello Worldサンプルの](/example-redirect?exampleName=hello-world)例で説明されている手順に従ってプロジェクトをセットアップする必要があります。

最初に、次を`src/lib.rs`ファイルに追加しましょう。

```typescript
// Add the wasm-pack crate
use wasm_bindgen::prelude::*;

// This exports an add function.
// It takes in two 32-bit integer values
// And returns a 32-bit integer value.
#[wasm_bindgen]
pub fn call_me_from_javascript(a: i32, b: i32) -> i32 {
  return add_integer_with_constant(a, b);
}

// A NOT exported constant
// Rust does not support exporting constants
// for Wasm (that I know of).
const ADD_CONSTANT: i32 = 24;

// A NOT exported function
// It takes in two 32-bit integer values
// And returns a 32-bit integer value.
fn add_integer_with_constant(a: i32, b: i32) -> i32 {
  return a + b + ADD_CONSTANT;
}
```

次に、 [wasm-pack](https://github.com/rustwasm/wasm-pack)を使用してコンパイルし、 `pkg/`ディレクトリを作成します。

```bash
wasm-pack build --release --target web
```

次に、 `index.js`ファイルを作成して、wasm出力をロードして実行します。 wasm-packによって生成された`pkg/exports.js`からwasm初期化モジュールをインポートしましょう。次に、wasm-packによって生成された`pkg/exports_bg.wasm` wasmファイルへのパスを渡すモジュールを呼び出しましょう。次に、エクスポートされた関数を呼び出して、エクスポートされなかった関数を調べましょう。

```javascript
import wasmInit from "./pkg/exports.js";

const runWasm = async () => {
  // Instantiate our wasm module
  const rustWasm = await wasmInit("./pkg/exports_bg.wasm");

  // Call the Add function export from wasm, save the result
  const result = rustWasm.call_me_from_javascript(24, 24);

  console.log(result); // Should output '72'
  console.log(rustWasm.ADD_CONSTANT); // Should output 'undefined'
  console.log(rustWasm.add_integer_with_constant); // Should output 'undefined'
};
runWasm();
```

最後に、 `index.html` ES6モジュールの`index.js` Javascriptファイルをロードします。そして、以下のデモ（ [ソースコード](/source-redirect?path=examples/exports/demo/rust) ）に似たものが得られるはずです！

---

## デモ

<iframe title="Rust Demo" src="/examples/exports/demo/rust/"></iframe>

次に、 [WebAssembly Linear Memoryを](/example-redirect?exampleName=webassembly-linear-memory)見てみましょう。
