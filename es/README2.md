# Hello World!

**Antes de comenzar con Rust and Wasm, asegúrese de revisar todos los idiomas disponibles, haciendo clic en el enlace "idiomas" en el encabezado.**

## Overview

Para nuestro primer programa, haremos un tipo de programa "Hola mundo" en [Rust](https://www.rust-lang.org/) y [wasm-pack](https://github.com/rustwasm/wasm-pack) .

Para simplificar las cosas con las limitaciones de Wasm mencionadas [en el ejemplo de introducción](/example-redirect?exampleName=introduction&programmingLanguage=all) , en lugar de mostrar una cadena, agregaremos dos números y mostraremos el resultado. Sin embargo, es bueno tener en cuenta que, en ejemplos posteriores, muchas de estas limitaciones serán eliminadas por el lenguaje de WebAssembly de su elección (en este caso, Rust). También se recomienda encarecidamente que eche un vistazo a la [Guía de inicio rápido de wasm-pack](https://github.com/rustwasm/wasm-pack#-quickstart-guide) , ya que se mencionará mucho en este ejemplo.

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

Then, let's compile our crate using wasm-pack, into a wasm module. Then run the following command, taking note of the [--target web](https://rustwasm.github.io/docs/wasm-pack/commands/build.html#target). The wasm-pack tool has support for a lot of different output types, especially for bundlers like Webpack or Rollup. But, since we want an ES6 module in our case, we use the `web` target below:

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
  const addResult = helloWorld.add(24, 24);

  // Set the result onto the body
  document.body.textContent = `Hello World! addResult: ${addResult}`;
};
runWasm();
```

Lastly, lets load our ES6 Module, `index.js` Javascript file in our `index.html`:

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

And we should have a working Wasm Add (Hello World) program! Congrats!

You should have something similar to the demo ([Source Code](/source-redirect?path=examples/hello-world/demo/rust)) below:

## Demo

<iframe title="Rust Demo" src="/examples/hello-world/demo/rust/"></iframe>

Next let's take a deeper look at WebAssembly [Exports](/example-redirect?exampleName=exports).
