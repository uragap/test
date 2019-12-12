# Export

## Visión de conjunto

En nuestro [ejemplo Hello World](/example-redirect?exampleName=hello-world) , llamamos a una función exportada desde WebAssembly, en nuestro Javascript. Profundicemos un poco más en las exportaciones y cómo se usan.

---

## Implementación

Si aún no lo ha hecho, debe configurar su proyecto siguiendo los pasos establecidos en el ejemplo de [Hello World Example](/example-redirect?exampleName=hello-world) .

Primero, agreguemos lo siguiente a nuestro archivo `src/lib.rs` :

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

Luego, compilemos eso usando [wasm-pack](https://github.com/rustwasm/wasm-pack) , que creará un directorio `pkg/` :

```bash
wasm-pack build --release --target web
```

A continuación, `index.js` un archivo `index.js` para cargar y ejecutar nuestra salida wasm. Importemos el módulo de inicialización wasm de `pkg/exports.js` que fue generado por wasm-pack. Luego, llamemos al módulo que pasa en la ruta a nuestro archivo wasm en `pkg/exports_bg.wasm` que fue generado por wasm-pack. Luego, sigamos adelante y llamemos a las funciones exportadas, y exploremos qué funciones NO se exportaron:

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

Por último, carguemos nuestro archivo ES6 Module, `index.js` Javascript en nuestro `index.html` . ¡Y debería obtener algo similar a la demostración ( [Código fuente](/source-redirect?path=examples/exports/demo/rust) ) a continuación!

---

## Manifestación

<iframe title="Rust Demo" src="/examples/exports/demo/rust/"></iframe>

A continuación, echemos un vistazo a la [memoria lineal de WebAssembly](/example-redirect?exampleName=webassembly-linear-memory) .

jiomujmoik,opijm9uijniujn9ikm0polk

agsrewhsarejhsrt
wrehaartj
szfgjsrg

srkjsfgkmxgh,msgh,kmtyh,kgdhmdb
