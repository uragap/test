# Hello World!

**Прежде чем приступить к работе с Rust и Wasm, обязательно ознакомьтесь со всеми доступными языками, нажав ссылку «языки» в заголовке.**

## обзор

Для нашей первой программы мы будем создавать программы типа «Hello world» на [Rust](https://www.rust-lang.org/) и [wasm-pack](https://github.com/rustwasm/wasm-pack) .

Для простоты ограничений Wasm, упомянутых [во вводном примере](/example-redirect?exampleName=introduction&programmingLanguage=all) , вместо отображения строки мы сложим вместе два числа и отобразим результат. Хотя следует иметь в виду, что в следующих примерах многие из этих ограничений будут абстрагированы выбранным вами языком WebAssembly (в данном случае Rust). Также настоятельно рекомен.

---

## Настройка проекта

Итак, во-первых, давайте [установим ржавчину](https://www.rust-lang.org/tools/install) , которая включает в себя [груз](https://doc.rust-lang.org/cargo/index.html) . Затем, используя cargo, давайте установим wasm-pack, который нам понадобится позже:

```bash
cargo install wasm-pack
```

Далее, давайте создадим нашу корзину для ржавчины в нашем текущем каталоге с использованием cargo

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

Наконец, давайте быстро заглянем внутрь каталога `src/` . Поскольку мы собираем библиотеку (lib) для использования в более крупном приложении, **нам нужно переименовать `src/main.rs` в `src/lib.rs`** Идите и сделайте это сейчас, прежде чем двигаться вперед.

Теперь, когда у нас есть настройки проекта и среды, давайте продолжим и приступим к фактической реализации.

---

## Реализация

Давайте продолжим и заменим `src/lib.rs` требуемым вызовом `use` как упомянуто в `src/lib.rs` , а также нашей функцией add:

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

Это выведет каталог `pkg/` содержащий наш модуль wasm, обернутый в объект js. `index.js` давайте создадим `index.js` JavaScript `index.js` и импортируем выведенный модуль ES6 в наш каталог `pkg/` . Затем мы вызовем нашу экспортированную функцию `add()` :

```javascript
// Import our outputted wasm ES6 module
// Which, export default's, an initialization function
import wasmInit from "./pkg/exports.js";

const runWasm = async () => {
  // Instantiate our wasm module
  const helloWorld = await wasmInit("./pkg/hello_world_bg.wasm");

  // the Add function export from wasm, save the result

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

Далее давайте более глубокий взгляд на WebAssembly [экспорт](/example-redirect?exampleName=exports) .
