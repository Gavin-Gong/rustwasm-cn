# Hello, World!

本节将向你展示如何构建并运行您的第一个 Rust 和 WebAssembly 程序 —— 一个弹出 "Hello, World!" 的网页。

确保你已经按照前面的 [设置说明](setup.html) 执行过。

## 克隆项目模板

该项目模板预先配置了明智的默认值，因此你可以快速构建、集成和打包 Web 代码。

用以下命令克隆项目模板：

```text
cargo generate --git https://github.com/rustwasm/wasm-pack-template
```

终端将提示您输入新项目的名称。我们将使用
**"wasm-game-of-life"**.

## 里面有什么

进入崭新的 `wasm-game-of-life` 项目并查看它的内容：

```text
wasm-game-of-life/
├── Cargo.toml
├── LICENSE_APACHE
├── LICENSE_MIT
├── README.md
└── src
    ├── lib.rs
    └── utils.rs
```

让我们详细地看一下其中的几个文件。

### `wasm-game-of-life/Cargo.toml`

`Cargo.toml` 文件为 Rust 的包管理器和编译工具 —— `cargo` 指定了依赖和元数据。
它预先配置了一个 `wasm-bindgen` 依赖项，我们稍后将深入讨论几个可选依赖项，
以及正确初始化生成 `.wasm` 库 的`crate-type`属性。

### `wasm-game-of-life/src/lib.rs`

`src/lib.rs` 是要编译成 WebAssembly 的 Rust create 的根文件。 它使用 `wasm-bindgen` 来和 JavaScript 交互。 这个文件引入 `window.alert` JavaScript 函数，并导出一个弹出问候信息的 `greet` Rust 函数。

```rust
extern crate cfg_if;
extern crate wasm_bindgen;

mod utils;

use cfg_if::cfg_if;
use wasm_bindgen::prelude::*;

cfg_if! {
    // 当 `wee_alloc` 特性开启时, 可以使用 `wee_alloc` 作为全局分配器
    if #[cfg(feature = "wee_alloc")] {
        extern crate wee_alloc;
        #[global_allocator]
        static ALLOC: wee_alloc::WeeAlloc = wee_alloc::WeeAlloc::INIT;
    }
}

#[wasm_bindgen]
extern {
    fn alert(s: &str);
}

#[wasm_bindgen]
pub fn greet() {
    alert("Hello, wasm-game-of-life!");
}

```

### `wasm-game-of-life/src/utils.rs`

`src/utils.rs` 模块提供了通用的工具程序来使得将 Rust 编译成 WebAssembly 更加容易。
在本教程的后面，我们将更加详细地了解其中一些工具程序，
例如我们在 [debugging our wasmcode](debug .html)中看到，但是我们现在可以忽略这个文件。

## 构建项目

我们 `wasm-pack` 来编排下面的构建步骤：

- 确保我们安装了 Rust 1.30 或者更高版本以及通过 `rustup` 安装了 `wasm32-unknown-unknown` target，
- 通过 `cargo` 编译将 Rust 源码成 WebAssembly `.wasm` 二进制文件，
- 使用 `wasm-bindgen`为 Rust 生成的 WebAssembly 程序生成 JavaScript API。

要完成以上步骤, 需要在项目目录下执行这个命令：

```
wasm-pack build
```

构建完成的时候，我们可以在 `pkg` 目录找到构建文件，应该有以下文件：

```
pkg/
├── package.json
├── README.md
├── wasm_game_of_life_bg.wasm
├── wasm_game_of_life.d.ts
└── wasm_game_of_life.js
```

`README.md` 文件是从主项目复制过来的，但是其它文件是全新的。

### `wasm-game-of-life/pkg/wasm_game_of_life_bg.wasm`

`.wasm` 文件是由 Rust 编译器将 Rust 源码编译生成的 WebAssembly 二进制文件。
它包含了所有我们所编写的 Rust 函数和数据的 wasm 版本。例如，它导出了一个 "greet" 函数。

### `wasm-game-of-life/pkg/wasm_game_of_life.js`

`.js` 文件由 `wasm-bindgen` 生成，它包含引入 DOM 和 JavaScript 函数到 Rust 以及暴露良好的 WebAssembly 函数 API 到 JavaScript 的 JavaScript 胶水。
例如, 这儿有一个封装了 `greet` 函数的 WebAssembly 模块导出了一个 `greet` JavaScript 函数。
到目前为止，粘合剂并没有做太多的事，但当我们 wasm 和 JavaScript 之间来回传递更多有趣的值，粘合剂将会帮助引导这些值越过边界。

```js
import * as wasm from "./wasm_game_of_life_bg";

// ...

export function greet() {
  return wasm.greet();
}
```

### `wasm-game-of-life/pkg/wasm_game_of_life.d.ts`

`.d.ts` 文件包含用于 JavaScript 粘合剂的 [TypeScript][] 类型声明。
如果你正使用 TypeScript，你可以对 WebAssembly 函数进行类型检查，你的 IDE 可以提供自动完成和建议的功能！
如果你不使用 TypeScript，你可以完全忽略这个文件。

```typescript
export function greet(): void;
```

[typescript]: http://www.typescriptlang.org/

### `wasm-game-of-life/pkg/package.json`

[`package.json` 文件包含 JavaScript 和 WebAssembly 包的元数据。][package.json]
它可以被 npm 和 JavaScript 打包器用来获取包的依赖，包名，版本，以及其他信息。
这个文件可以跟好地和 JavaScript 工具集成，并且允许我们发布我们的包到 npm。

```json
{
  "name": "wasm-game-of-life",
  "collaborators": ["Your Name <your.email@example.com>"],
  "description": null,
  "version": "0.1.0",
  "license": null,
  "repository": null,
  "files": ["wasm_game_of_life_bg.wasm", "wasm_game_of_life.d.ts"],
  "main": "wasm_game_of_life.js",
  "types": "wasm_game_of_life.d.ts"
}
```

[package.json]: https://docs.npmjs.com/files/package.json

## 在 Web 页面中使用它

为了将我们的 `wasm-game-of-life` 包用到 Web 页面中，我们使用 [
`create-wasm-app` JavaScript 项目模板][create-wasm-app].

[create-wasm-app]: https://github.com/rustwasm/create-wasm-app

在 `wasm-game-of-life` 目录下运行下面的命令：

```
npm init wasm-app www
```

我们崭新的子目录 —— `wasm-game-of-life/www` 包含了以下文件：

```
wasm-game-of-life/www/
├── bootstrap.js
├── index.html
├── index.js
├── LICENSE-APACHE
├── LICENSE-MIT
├── package.json
├── README.md
└── webpack.config.js
```

再一次, 让我们仔细瞅瞅目录下的这些文件。

### `wasm-game-of-life/www/package.json`

`package.json` 包含了 `webpack` 和 `webpack-dev-server` 依赖的一些预配置,
也包括了 `hello-wasm-pack` 这个已经发布到 npm 的 `wasm-pack-template` 初始版本包。

### `wasm-game-of-life/www/webpack.config.js`

该 file 配置了 webpack 和它的本地开发服务器。它包含一些预配置, 你不需要修改它就可以让 webpack 和本地开发服务器起工作。

### `wasm-game-of-life/www/index.html`

这是网页的根 HTML 文件。 它除了加载 `bootstrap.js` 这个轻量封装 `index.js` 的文件外，并没有做太多其他的工作。

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>Hello wasm-pack!</title>
  </head>
  <body>
    <script src="./bootstrap.js"></script>
  </body>
</html>
```

### `wasm-game-of-life/www/index.js`

`index.js` 是我们 Web 页面的 JavaScript 主入口。
它引入了 `hello-wasm-pack` npm 包，这个包默认包含了 `wasm-pack-template` 编译 WebAssembly 和 JavaScript 的粘合剂，
然后它调用了 `hello-wasm-pack` 的 `greet` 函数。

```js
import * as wasm from "hello-wasm-pack";

wasm.greet();
```

### 安装依赖

首先，确保本地开发服务器 和 它的依赖已经通过在 `wasm-game-of-life/www` 子目录运行 `npm install` 安装完成了：

```text
npm install
```

这个命令只需要运行一次就会安装 `webpack` JavaScript 打包器和它的开发服务器。

> 记住 `webpack` 不是必须和 Rust 和 WebAssembly 一起配合使用。
> 他只是一个我们为了方便采用的打包器和开发服务器。
> Parcel 和 Rollup 应该也支持将 WebAssembly 导入为 ECMAScript 模块

### 在 `www` 目录，使用我们的本地 `wasm-game-of-life` 包

我们不要使用 npm `hello-wasm-pack` 包, 而是使用我们本地的 `wasm-game-of-life` 包。 这将让我们能快速开发我们的 Game of Life 程序。

首先, 运行在 `wasm-game-of-life/pkg` 目录 `npm link` , 这样的话本地包可以依赖于其他本地包而不用发布到 npm:

```bash
npm link
```

> 🐞 当你运行 `npm link`的时候，你碰到 `EACCESS` 或者权限错误 ?
> [如何 避免 `npm`的权限错误 ？](https://docs.npmjs.com/getting-started/fixing-npm-permissions)

其次，通过在 `wasm-game-of-life/www` 目录下运行下面的命令，
就可以从 `www` 包使用 `npm link`之后的 `wasm-game-of-life`：

```
npm link wasm-game-of-life
```

最后, 修改 `wasm-game-of-life/www/index.js`，使之引入 `wasm-game-of-life`包
而不是`hello-wasm-pack` 包:

```js
import * as wasm from "wasm-game-of-life";

wasm.greet();
```

我们的 Web 页面已经可以通过本地服务访问了!

## 本地服务

接下来，为本地服务器打开终端。在新的窗口运行本地服务器让我们可以使之在后台运行，并且不会阻止在同时运行其他命令。
在新的终端窗口中的 `wasm-game-of-life/www` 目录下， 运行这个命令：

```
npm run start
```

从你的浏览器访问 [http://localhost:8080/](http://localhost:8080/)
然后你会看到一条问候语。

[!["Hello, wasm-game-of-life!" Web 页面弹窗截图](../images/game-of-life/hello-world.png)](../images/game-of-life/hello-world.png)

无论何时，你只要做出修改，希望反应在
[http://localhost:8080/](http://localhost:8080/)上, 只要在 `wasm-game-of-life` 目录重新运行 `wasm-pack build` 命令。

## 练习

- 修改 `wasm-game-of-life/src/lib.rs` 的 `greet` 函数，使之接受 `name: &str` 参数来自定义提示信息，然后在 `wasm-game-of-life/www/index.js` 中传入你的名字到 `greet` 函数。使用 `wasm-pack build` 重新构建 `.wasm` 二进制文件，然后在你的 Web 浏览器中刷新 [http://localhost:8080/](http://localhost:8080/)，然后你将会看到一条定制的问候语。

  <details>
    <summary>答案</summary>

  `wasm-game-of-life/src/lib.rs` 中的新版 `greet` 函数:

  ```rust
  #[wasm_bindgen]
  pub fn greet(name: &str) {
      alert(&format!("Hello, {}!", name));
  }
  ```

  在 `wasm-game-of-life/www/index.js` 中调用 `greet`:

  ```js
  wasm.greet("Your Name");
  ```

  </details>
