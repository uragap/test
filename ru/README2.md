# Hello World!

**Before getting started with Rust and Wasm, be sure to check out all of the languages available, by clicking the "languages" link in the header.**

## Overview

For our first program, we will be doing a "Hello world" type of program in [Rust](https://www.rust-lang.org/) and [wasm-pack](https://github.com/rustwasm/wasm-pack).

Для простоты использования ограничений Wasm, упомянутых [во вводном примере](/example-redirect?exampleName=introduction&programmingLanguage=all) , вместо отображения строки мы сложим вместе два числа и отобразим результат. Хотя следует иметь в виду, что в следующих примерах многие из этих ограничений будут абстрагированы выбранным вами языком WebAssembly (в данном случае Rust). Также настоятельно рекомен.

---

## Project Setup

So first, Let's get [rust installed](https://www.rust-lang.org/tools/install), which includes [cargo](https://doc.rust-lang.org/cargo/index.html). Then, using cargo, let's install wasm-pack, which we will need later:

```bash
cargo install wasm-pack
```

Next, let's create our rust crate in our current directory using cargo:

```bash
cargo init
```

Then, let's edit our new `Cargo.toml` to implement [wasm-pack](https://github.com/rustwasm/wasm-pack#-quickstart-guide) as mentioned in their quickstart:

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

Lastly, let's take a quick peek inside at the `src/` directory. Since we are building a library (lib) to be used by a larger application, **we need to rename the `src/main.rs` to `src/lib.rs`.** Go ahead and do that now before moving forward.

Now that we have our project and environment setup, let's go ahead and start the actual implementation.

---

## Implementation

Let's go ahead and replace `src/lib.rs` with the required `use` call as mentioned in the quickstart, as well as our add function:

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

Затем давайте скомпилируем наш ящик, используя wasm-pack, в модуль wasm. Затем выполните следующую команду, [обращая](https://rustwasm.github.io/docs/wasm-pack/commands/build.html#target) внимание на {a1}--target web{/a1} . Инструмент wasm-pack поддерживает множество различных типов вывода, особенно для таких пакетов, как Webpack или Rollup. Но, поскольку мы хотим модуль ES6 в нашем случае, мы используем {code2}web{/code2} цель ниже:

```bash
wasm-pack build --release --target web
```

This will output a `pkg/` directory containing our wasm module, wrapped in a js object. Next, lets create an `index.js` JavaScript file, and import the outputted ES6 module in our `pkg/` directory. Then, we will call our exported `add()` function:

```javascript
// Import our outputted wasm ES6 module
// Which, export default's, an initialization function
import wasmInit from "./pkg/exports.js";

const runWasm = async () => {
  // Instantiate our wasm module
  const helloWorld = await wasmInit("./pkg/hello_world_bg.wasm");

  // Call the Add function export from wasm, save the result

  document.body.textContent = `Hello World! addResult: ${addResult}`;
};
runWasm();
```

Наконец, давайте загрузим наш модуль `index.js` Javascript-файл `index.js` в наш {code2}inde

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

And w

You should have something similar to the demo ([Source Code](/source-redirect?path=examples/hello-world/demo/rust)) below:

## Demo

<iframe title="Rust Demo" src="/examples/hello-world/demo/rust/"></iframe>

Next let's take a deeper look at WebAssembly [Exports](/example-redirect?exampleName=exports).
