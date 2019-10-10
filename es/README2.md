# Hola Mundo!

**Antes de comenzar con Rust and Wasm, asegúrese de revisar todos los idiomas disponibles, haciendo clic en el enlace "idiomas" en el encabezado.**

## Visión de conjunto

Para nuestro primer programa, haremos un tipo de programa "Hola mundo" en [Rust](https://www.rust-lang.org/) y [wasm-pack](https://github.com/rustwasm/wasm-pack) .

Para simplificar las cosas con las limitaciones de Wasm mencionadas [en el ejemplo de introducción](/example-redirect?exampleName=introduction&programmingLanguage=all) , en lugar de mostrar una cadena, agregaremos dos números y mostraremos el resultado. Sin embargo, es bueno tener en cuenta que, en ejemplos posteriores, muchas de estas limitaciones serán eliminadas por el lenguaje de elección de WebAssembly (en este caso, Rust). También se recomienda encarecidamente que eche un vistazo a la [Guía de inicio rápido de wasm-pack](https://github.com/rustwasm/wasm-pack#-quickstart-guide) , ya que se hará una gran referencia en este ejemplo.

---

## Configuración del proyecto

Primero, instalemos [óxido](https://www.rust-lang.org/tools/install) , que incluye [carga](https://doc.rust-lang.org/cargo/index.html) . Luego, usando carga, instalemos wasm-pack, que necesitaremos más adelante:

```bash
cargo install wasm-pack
```

A continuación, creemos nuestra caja de óxido en nuestro directorio actual usando carga:

```bash
cargo init
```

Luego, editemos nuestro nuevo `Cargo.toml` para implementar [wasm-pack](https://github.com/rustwasm/wasm-pack#-quickstart-guide) como se menciona en su inicio rápido:

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

Por último, echemos un vistazo rápido al directorio `src/` . Como estamos creando una biblioteca (lib) para que la use una aplicación más grande, **necesitamos cambiar el nombre de `src/main.rs` a `src/lib.rs`** Adelante, hazlo ahora antes de seguir adelante.

Ahora que tenemos nuestra configuración de proyecto y entorno, sigamos adelante y comencemos la implementación real.

---

## Implementación

`src/lib.rs` y reemplacemos `src/lib.rs` con la llamada de `use` requerida como se menciona en el inicio rápido, así como nuestra función add:

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

Luego, compilemos nuestra caja usando wasm-pack, en un módulo wasm. Luego ejecute el siguiente comando, tomando nota de la [web --target](https://rustwasm.github.io/docs/wasm-pack/commands/build.html#target) . La herramienta wasm-pack es compatible con muchos tipos de salida diferentes, especialmente para paquetes como Webpack o Rollup. Pero, dado que queremos un módulo ES6 en nuestro caso, utilizamos el objetivo `web` a continuación:

```bash
wasm-pack build --release --target web
```

Esto generará un directorio `pkg/` contiene nuestro módulo wasm, envuelto en un objeto js. A continuación, `index.js` un archivo `index.js` JavaScript e importemos el módulo ES6 generado en nuestro directorio `pkg/` . Luego, llamaremos a nuestra función exportada `add()` :

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

Por último, carguemos nuestro módulo ES6, archivo Javascript `index.js` en nuestro `index.html` :

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

¡Y deberíamos tener un programa Wasm Add (Hello World) en funcionamiento! Felicidades!

Debería tener algo similar a la demostración ( [Código fuente](/source-redirect?path=examples/hello-world/demo/rust) ) a continuación:

## Manifestación

<iframe title="Rust Demo" src="/examples/hello-world/demo/rust/"></iframe>

A continuación, echemos un vistazo más profundo a las [exportaciones de](/example-redirect?exampleName=exports) WebAssembly.
