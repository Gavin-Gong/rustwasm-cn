# 什么是 WebAssembly？

WebAssembly (wasm)是一种简单的机器模型和可拓展规范下的可执行格式。它被设计成可移植的、紧凑的和接近甚至以原生速度运行的性能。

作为一种编程语言，WebAssembly 由两种表示相同结构的格式组成，虽然方式不同:

1. `.wat` 文本格式 (`wat` 是 "**W**eb**A**ssembly **T**ext" 的缩写) 使用
   [S-expressions], 与 Lisp 系语言例如 Schema 和 Clojure 有诸多相似之处。

2. `.wasm` 二进制格式偏底层，能直接被 wasm 虚拟机所使用。它在概念上类似 ELF 和 Mach-O。

作为参考，这里有一个用 `wat` 写的阶乘函数:

```
(module
  (func $fac (param f64) (result f64)
    get_local 0
    f64.const 1
    f64.lt
    if (result f64)
      f64.const 1
    else
      get_local 0
      get_local 0
      f64.const 1
      f64.sub
      call $fac
      f64.mul
    end)
  (export "fac" (func $fac)))
```

如果你对 `wasm` 文件是什么样的感兴趣，可以在 [wat2wasm demo] 使用上述代码。

## 线性内存

WebAssembly 有一个非常简单的[内存模型]。wasm 模块可以访问一块由一个字节的扁平数组组成的 "线性内存"。这块[可增长内存]的大小是页大小(64K)的倍数。它不能缩小。

## WebAssembly 只适用 Web 吗？

大体上讲，尽管 WebAssembly 目前已经在 JavaScript 和 Web 社区引起了关注，但是 wasm 对宿主环境不做任何限定。
因此，有理由推测 wasm 将会是一种 "可移植的，可执行的" 的格式，将来会在各种上下文中使用。
然而，在今天，wasm 主要与 JavaScript (JS)有关，后者有很多偏向(包括 Web 和[Node.js])。

[内存模型]: https://webassembly.github.io/spec/core/syntax/modules.html#syntax-mem
[可增长内存]: https://webassembly.github.io/spec/core/syntax/instructions.html#syntax-instr-memory
[extensive specification]: https://webassembly.github.io/spec/
[value types]: https://webassembly.github.io/spec/core/syntax/types.html#value-types
[node.js]: https://nodejs.org
[s-expressions]: https://en.wikipedia.org/wiki/S-expression
[wat2wasm demo]: https://cdn.rawgit.com/WebAssembly/wabt/aae5a4b7/demo/wat2wasm/
