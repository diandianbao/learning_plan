# learning_plan

个人学习进度归档仓库。所有学习主题的 checkpoint 统一存放在这里，作为召回引擎（未来的手机 app）的唯一事实源。

## 归档来源

- **电脑端**：通过 code agent 的 `learning-checkpoint` skill，对 agent 说"保存进度"/"同步"即可生成并推送
- **手机端**（规划中）：语音记录（app 端用系统能力离线转写）、草稿纸/书页照片，原始材料 push 到 `attachments/`；之后对电脑端 agent 说"消化归档"，由 `learning-digest` skill 补总结生成 checkpoint

## 原始材料格式

手机端每次采集生成 `attachments/` 下的一个条目目录：

```
attachments/YYYY-MM-DD-HHMM/
  meta.md         # frontmatter: created、topic（可空）、digested: false
  transcript.md   # 语音转写文本（可选）
  *.jpg / *.png   # 草稿纸、书页照片（可选）
```

`learning-digest` 消化后会生成对应 checkpoint（`## 上下文` 引用附件相对路径），并把 `meta.md` 的 `digested` 置为 `true`。消化完成且转写确认无误后，音频文件会被删除（内容已由 `transcript.md` 承载），照片和转写文本永久保留——照片是召回时的记忆资产。

## Checkpoint 格式

每个 checkpoint 是 `checkpoints/` 下的一个 markdown 文件，文件名为 `YYYY-MM-DD-HHMM-<主题slug>.md`，结构如下：

```markdown
---
topic: LLVM                # 学习主题
status: active             # active | dormant | archived
source: code-agent         # code-agent | voice | photo
created: 2026-07-29T22:30:00+08:00
---

## 在做什么
一句话概括学习主题和当前位置（教材章节、题目编号、代码模块）。

## 下一步动作
一个具体、可立即执行的动作。不写"继续学 LLVM"，写"动手把 mem2reg 的 pass 源码读一遍并画出变量重命名的流程"。

## 悬而未决的问题
当时卡住或好奇的问题。优先保留自己的原话——召回时这是最强的记忆钩子。

## 上下文
- 涉及的文件路径、资料链接、题号、章节页码等
```

## 设计原则

- **记录成本压到 30 秒以内**：agent 起草，人只确认
- **下一步动作必须具体**：恢复状态的成本主要在"想不起接下来干嘛"
- **悬而未决的问题用原话**：利用蔡格尼克效应，未完成的任务会在脑内后台运行
