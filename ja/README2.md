# "こんにちは世界"

**RustとWasmを使い始める前に、ヘッダーの「言語」リンクをクリックして、使用可能なすべての言語を必ずチェックアウトしてください。**

## 概要

最初のプログラムでは、 [Rust](https://www.rust-lang.org/)と[wasm-packで](https://github.com/rustwasm/wasm-pack) 「Hello world」タイプのプログラムを[実行します](https://github.com/rustwasm/wasm-pack) 。

[導入例](/example-redirect?exampleName=introduction&programmingLanguage=all)で述べ[た](/example-redirect?exampleName=introduction&programmingLanguage=all) Wasmの制限で物事を単純にするために、文字列を表示する代わりに、2つの数字を加算して結果を表示します。ただし、後の例では、これらの制限の多くが、選択したWebAssembly言語（この場合はRust）によって抽象化されることに留意してください。また、この例では多く参照されるため、 [wasm-pack QuickStart Guideを](https://github.com/rustwasm/wasm-pack#-quickstart-guide)参照することを強くお勧めします。

---

## プロジェクトのセットアップ

それではまず、 [貨物](https://doc.rust-lang.org/cargo/index.html)を含む[rustをインストールし](https://www.rust-lang.org/tools/install)ましょう。次に、cargoを使用して、後で必要になるwasm-packをインストールしましょう。

```bash
cargo install wasm-pack
```

次に、貨物を使用して現在のディレクトリに錆箱を作成します。

```bash
cargo init
```

次に、クイックスタートで述べたように、新しい`Cargo.toml`を編集して[wasm-pack](https://github.com/rustwasm/wasm-pack#-quickstart-guide)を実装しましょう。

```toml
[package]
name = "hello-world"
version = "0.1.0"
authors = ["Your Name <your@name.com>"]
edition = "2018"

[lib]
crate-type = ["cdylib"]

[dependencies]
wasm-bind
```

最後に、 `src/`ディレクトリを簡単に見てみましょう。私たちは、より大きなアプリケーションで使用するライブラリ（LIB）を構築しているので、 **私たちは、名前を変更する必要がある`src/main.rs`する`src/lib.rs` 。**先に進んで、今それを実行してから先に進みます。

プロジェクトと環境のセットアップが完了したので、実際の実装を始めましょう。

---

## 実装

先に進み、 `src/lib.rs`を、クイックスタートで`src/lib.rs`した必須の`use`呼び出しとadd関数に置き換えてみましょう：

```rust
// Add the wasm-pack crate
use wasm_bindgen::prelude::*;

// Our Add function
// wasm-pack requires "exported" functions
// to include #[wasm_bindgen]
#[wasm_bindgen]
pub fn add(a: i32, b: i32) -> i32 {
  return a + b;
}
```

次に、wasm-packを使用してクレートをwasmモジュールにコンパイルします。次に、-- [target webに](https://rustwasm.github.io/docs/wasm-pack/commands/build.html#target)注意して、次のコマンドを実行します。 wasm-packツールは、特にWebpackやRollupなどのバンドラー向けに、さまざまな出力タイプをサポートしています。ただし、今回のケースではES6モジュールが必要なので、以下の`web`ターゲットを使用します。

```bash
wasm-pack build --release --target web
```

これは、jsオブジェクトにラップされたwasmモジュールを含む`pkg/`ディレクトリを出力します。次に、 `index.js` JavaScriptファイルを作成し、出力されたES6モジュールを`pkg/`ディレクトリにインポートします。次に、エクスポートされた`add()`関数を呼び出します。

```javascript
// Import our outputted wasm ES6 module
// Which, export default's, an initialization function
import wasmInit from "./pkg/exports.js";

const runWasm = async () => {
  // Instantiate our wasm module
  const helloWorld = await wasmInit("./pkg/hello_world_bg.wasm");

  // Call the Add function export from wasm, save the result
  const addResult = helloWorld.add(24, 24);

  // Set the result onto the body
  document.body.textContent = `Hello World! addResult: ${addResult}`;
};
runWasm();
```

最後に、 `index.html` ES6モジュールの`index.js` Javascriptファイルをロードします。

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title>Hello World - Rust</title>
    <script type="module" src="./index.js"></script>
  </head>
  <body></body>
</html>
```

そして、有効なWasm Add（Hello World）プログラムが必要です！おめでとうございます！

以下のデモ（ [ソースコード](/source-redirect?path=examples/hello-world/demo/rust) ）に似たものが必要です。

## デモ

<iframe title="Rust Demo" src="/examples/hello-world/demo/rust/"></iframe>

次に、WebAssembly [Exportsを](/example-redirect?exampleName=exports)詳しく見てみましょう。
