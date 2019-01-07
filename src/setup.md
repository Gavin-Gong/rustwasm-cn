# 起步

如果你想要使用 Rust 开发 wasm，你需要先设置你的开发环境，如果还没有的话，你需要先安装[rustup][rustup] (官方工具)来安装和管理 Rust 编译器版本。按照网站的说明将其安装到你的机器。wasm 场景下开发的话，你需要暂时将 Rust 切换到 nightly 版本。

```bash
$ rustup default nightly
```

一旦安装完成，你需要获取 `wasm32-unknown-unknown` 工具链。

```bash
$ rustup target add wasm32-unknown-unknown --toolchain nightly
```

接下来，如果你对生成小的 wasm 二进制文件感兴趣，你可能需要安装 [wasm-gc][wasm-gc] 工具来生成更小的二进制文件，并解决目前编译器工具链的 bug:

```bash
$ cargo install wasm-gc
```

最后，如果你真的对生成小的 wasm 二进制文件感兴趣，你会从 [binaryen 工具包][binaryen] 中安装 `wasm-opt`。

[rustup]: https://www.rustup.rs/
[binaryen]: https://github.com/WebAssembly/binaryen
[wasm-gc]: https://github.com/alexcrichton/wasm-gc
