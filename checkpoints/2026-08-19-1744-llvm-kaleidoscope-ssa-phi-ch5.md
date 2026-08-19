---
topic: llvm
status: active
source: code-agent
created: 2026-08-19T17:44:40+0800
---

## 在做什么

LLVM Kaleidoscope 第 5 章（控制流）学习启动，重点攻克 SSA/PHI——已系统理解 SSA（诞生背景：数据流分析困境；核心：def-use 链编码进变量名；phi 语义：按来源块选值；对优化的支撑：常量传播/死代码/寄存器分配/循环携带依赖识别），并确定 codegen 下"控制流汇合处手动 CreatePHI"是下一步实操重点。还澄清了 JIT 产出的是内存机器码而非可执行文件、KaleidoscopeJIT 通过 getTargetTriple 确定平台。

## 下一步动作

实现第 5 章控制流：① Lexer 加 `tok_if/then/else/for/in` 关键字；② Parser 加 `ParseIfExpr`/`ParseForExpr` 接入 `ParsePrimary`；③ Codegen 实现 `IfExprAST::codegen`（fcmp + br + then/else/merge 三个基本块 + CreatePHI 填两条 incoming）和 `ForExprAST::codegen`（循环头 phi + 条件跳转），跑通 `def fib(x) if x<3 then 1 else fib(x-1)+fib(x-2); fib(10);`。

## 悬而未决的问题

- 自测题没答："a*b+c*d 会被解析成什么树？它不需要任何'让位'递归就能解析对吗？为什么？"
- 第 3 章已知 bug 未修：`extern foo(a)` 后 `def foo(b)` 不校验签名（第 4 章"每函数一模块"后表现可能变了，值得重新验证）
- 循环 phi 的优化细节（依赖分析、向量化）决定"等后面看多了再慢慢理解"，今天 SSA 文档已补大部分背景

## 上下文

- 教程：https://llvm.org/docs/tutorial/MyFirstLanguageFrontend/ （LangImpl01-04 完成，第 5 章控制流进行中）
- 代码：`/Users/looboo/myprojects/llvm/experiments/chapter01/`（`include/` + `src/` 模块化，KaleidoscopeJIT 已接入）
- 参考实现：`/Users/looboo/opensource/llvm-project/llvm/examples/Kaleidoscope/Chapter5/toy.cpp`（LLVM 17.0.1，macOS arm64）
- 今天系统学习的 SSA 详解资料（会话内整理：背景/Cytron 论文/5 个优化案例/支配边界）
