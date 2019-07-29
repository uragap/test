# экспорт

## обзор

В нашем [примере Hello World](/example-redirect?exampleName=hello-world) мы вызвали функцию, экспортированную из WebAssembly, в нашем Javascript. Давайте немного углубимся в экспорт и то, как он используется.

---

## Реализация

Если вы еще этого не сделали, вы должны настроить свой проект, следуя инструкциям, приведенным в [примере Hello World Example](/example-redirect?exampleName=hello-world) .

Сначала добавим следующее в наш файл `src/lib.rs` :

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

Затем давайте скомпилируем это, используя [wasm-pack](https://github.com/rustwasm/wasm-pack) , который создаст каталог `pkg/` :

```bash
wasm-pack build --release --target web
```

`index.js` давайте создадим файл `index.js` для загрузки и запуска вывода wasm. Давайте импортируем модуль инициализации wasm из `pkg/exports.js` который был сгенерирован wasm-pack. Затем, давайте вызовем модуль, передающий путь к нашему файлу wasm в `pkg/exports_bg.wasm` который был сгенерирован wasm-pack. Затем давайте продолжим и вызовем экспортированные функции, а также рассмотрим, какие функции НЕ экспортировались:

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

Наконец, давайте загрузим наш модуль `index.js` Javascript-файл `index.js` в наш `index.html` . И вы должны получить что-то похожее на демо ( [исходный код](/source-redirect?path=examples/exports/demo/rust) ) ниже!

---

## демонстрация

<iframe title="Rust Demo" src="/examples/exports/demo/rust/"></iframe>

Далее давайте посмотрим на [линейную память WebAssembly](/example-redirect?exampleName=webassembly-linear-memory) .
