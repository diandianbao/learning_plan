---
topic: llvm
status: active
source: code-agent
created: 2026-08-18T21:16:26+0800
---

## 在做什么

LLVM Kaleidoscope 教程第 3-4 章完成并实现：codegen（AST→LLVM IR）、优化管线（InstCombine/Reassociate/GVN/CFGSimplify 四个 pass）、JIT 执行（KaleidoscopeJIT），demo 已能真正运行 Kaleidoscope 程序（`4+5` 算出 9、`foo(2)` 算出 3、`sin(1.0)` 调 libm、`putchard(120)` 打印字符、跨模块调用 `bar(3,4)=7`）。同时梳理了大量概念：SSA/Value、Module 符号表、"LLVMContext 是总账本不是优化上下文"、AnalysisManager 统一模板分层缓存、JIT 是编译不是运行、IR 是"带类型的汇编 DSL"、LTO/pass 排序的局部最优问题、GraalVM 背景。

## 下一步动作

开始第 5 章控制流：① Lexer 加 `tok_if/then/else/for/in` 关键字；② Parser 加 `ParseIfExpr`/`ParseForExpr` 接入 `ParsePrimary`；③ Codegen 实现 `IfExprAST::codegen`（fcmp + br + then/else/merge 三个基本块 + PHI 合并）和 `ForExprAST::codegen`，跑通 `def fib(x) if x<3 then 1 else fib(x-1)+fib(x-2); fib(10);`。

## 悬而未决的问题

- 自测题没答："a*b+c*d 会被解析成什么树？它不需要任何'让位'递归就能解析对吗？为什么？"
- 第 3 章那个已知 bug 还没修：`extern foo(a)` 后 `def foo(b)` 不校验签名（第 4 章"每函数一个模块"后这个 bug 的表现可能变了，值得重新验证）
- 对优化 pass"每个层级缓存什么"还想真正优化一次才有感觉
- 吐槽官方教程跳跃性大"懂的都懂，不会的听不懂"——对策是逐段翻译 + 追问概念 + 动手实现，这个学法有效

## 上下文

- 教程：https://llvm.org/docs/tutorial/MyFirstLanguageFrontend/ （LangImpl01-04 已完成，下一篇 LangImpl05 控制流）
- 代码：`/Users/looboo/myprojects/llvm/experiments/chapter01/`（`include/AST.h`、`Codegen.h`、`KaleidoscopeJIT.h`；`src/Lexer.cpp`、`Parser.cpp`、`Codegen.cpp`、`main.cpp`）
- 参考实现：`/Users/looboo/opensource/llvm-project/llvm/examples/Kaleidoscope/Chapter4/toy.cpp`（LLVM 17.0.1，macOS arm64）
