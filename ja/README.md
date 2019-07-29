# 輸出

## 概要

この[Hello Worldの例](/example-redirect?exampleName=hello-world)では、JavascriptのWebAssemblyからエクスポートされた関数を呼び出しました。輸出とその使用方法についてもう少し詳しく説明しましょう。

---

## 実装

まだ行っていない場合は、 [Hello Worldの例の](/example-redirect?exampleName=hello-world)例に示されている手順に従ってプロジェクトを設定する必要があります。

まず、 `src/lib.rs`ファイルに以下を追加しましょう。

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

それでは、 [wasm-pack](https://github.com/rustwasm/wasm-pack)を使用してコンパイルしましょう。これにより、 `pkg/`ディレクトリが作成されます。

```bash
wasm-pack build --release --target web
```

次に、wasm出力を読み込んで実行するための`index.js`ファイルを作成しましょう。 wasm-packによって生成された`pkg/exports.js`からwasm初期化モジュールをインポートしましょう。それでは、wasm-packによって生成された`pkg/exports_bg.wasm`あるwasmファイルへのパスを渡すモジュールを呼び出しましょう。それでは、先に進んでエクスポートされた関数を呼び出し、エクスポートされなかった関数を調べてみましょう。

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

最後に、ES6モジュールの`index.js` Javascriptファイルを`index.html`ロードします。そして、あなたは以下のデモ（ [ソースコード](/source-redirect?path=examples/exports/demo/rust) ）のようなものを手に入れるべきです！

---

## デモ

<iframe title="Rust Demo" src="/examples/exports/demo/rust/"></iframe>

次に、 [WebAssembly Linear Memoryを](/example-redirect?exampleName=webassembly-linear-memory)見てみましょう。
