---
topic: llvm
status: active
source: code-agent
created: 2026-08-05T16:23:07+0800
---

## 在做什么

学习 LLVM 官方 Kaleidoscope 教程（My First Language Frontend），已完成第 1-2 章——Lexer、AST、Parser（递归下降 + 运算符优先级解析）和 REPL driver。代码在 `experiments/chapter01`，按 `include/` + `src/` 模块化组织。

## 下一步动作

开始第 3 章 codegen：先改 `CMakeLists.txt` 用 `llvm-config` 链接 LLVM 库，再给 `AST.h` 每个节点加 `virtual codegen()` 方法，用 `IRBuilder` 生成 LLVM IR，跑通 `def foo(x) x+1` 输出 IR 的第一版。

## 悬而未决的问题

- "a*b+c*d 会被解析成什么树？它不需要任何'让位'递归就能解析对吗？为什么？"——自测题还没答
- 第 3 章 codegen 需要的全局状态（`TheContext` / `Builder` / `NamedValues` 符号表）在模块化拆分下放哪、怎么跨文件共享，方案还没定

## 上下文

- 教程：https://llvm.org/docs/tutorial/MyFirstLanguageFrontend/LangImpl02.html （第 2 章已完成，下一篇 LangImpl03）
- 代码：`/Users/looboo/myprojects/llvm/experiments/chapter01/`（`include/Lexer.h`、`AST.h`、`Parser.h`；`src/Lexer.cpp`、`Parser.cpp`、`main.cpp`）
- 参考实现：`/Users/looboo/opensource/llvm-project/llvm/examples/Kaleidoscope/Chapter2/toy.cpp`（LLVM 17.0.1）
- 环境：macOS，C++20，CMake
