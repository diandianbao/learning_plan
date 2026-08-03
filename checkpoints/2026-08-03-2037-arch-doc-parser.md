---
topic: arch-doc-parser
status: active
source: code-agent
created: 2026-08-03T20:37:24+0800
---

## 在做什么

设计并验证"架构设计规范 AI-native 化"方案：把几百篇历史架构规范（Word/PPT/Excel/PDF）转成 code agent 可理解的知识库。方案已成型并有美团文章样本全链路验证（1 篇 → 20 条 decisions + 15 图转写 + 图 IR + D2 模型）。核心共识：**grep-native 消费（不做 RAG）**、**写时多模态读时纯文本（图片转写+IR 双产出）**、**Git 唯一 source of truth（MkDocs 静态站点做人类展示层）**、**四层验证框架**。

## 下一步动作

1. **做 PPT 给别人介绍思路**：`presentation/arch-kb-slides.md` 已有 11 页 Marp 版本，需要导出为可交付的 PPTX 成品（或用 Marp 直接放映），确定讲解口径后给他人介绍方案
2. **找 1-2 个领域验证思路**：从真实业务领域各挑 1-2 篇代表性文档（覆盖 Word/PPT/PDF 等不同格式），走完整流水线（解析 → 图片转写/IR → 知识抽取 → 编译），验证方案在真实存量文档上的可行性

## 悬而未决的问题

- grep-native 的词汇鸿沟是否真的会漏检？"agent 搜'容量上限'而文档写的是别的措辞"这个风险目前靠转写富集关键词缓解，但**端到端验收还没跑，心里没底**——如果漏检显著，向量索引就要作为补丁回归
- 真实存量文档（PDF/Word）的解析坑还没碰过：Docling 对不同格式的版面还原质量、老格式 .doc/.ppt 的转换链路，都未验证
- 文档视图最终用什么工具展示还没定论（倾向 MkDocs Material，但"catalog→导航、yaml→表格"的构建脚本骨架还没搭）

## 上下文

- 方案文档：`docs/ai-native-arch-knowledge-base.md`
- 流水线：`pipeline/`（fetch_html.py / describe_images.md / extract_knowledge.md / compile_decisions.py / compile_model.py）
- 样例知识库：`kb/`（美团数据库容量评估系统，catalog.yaml + specs + decisions/raw+compiled + model/raw+合并 + D2）
- 演示材料：`presentation/arch-kb-slides.md`（11 页 Marp 幻灯片）
- 参考源文：https://tech.meituan.com/2025/10/17/Database-Capacity-Evaluation-System.html
