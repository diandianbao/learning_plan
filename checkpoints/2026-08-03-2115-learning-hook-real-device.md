---
topic: learning_hook
status: active
source: code-agent
created: 2026-08-03T21:15:07+0800
---

# learning_hook app v1 真机落地：路径契约统一 + URLEncoder 兼容修复

## 在做什么

app v1 四个里程碑已在模拟器全部联调通过，本轮把 app 装到华为真机（HBN-AL00）并修复两个真实环境才暴露的问题。

① **附件路径契约统一**：发现 learning-checkpoint skill 没写附件约定，导致归档时把截图放到了 `checkpoints/assets/` 并引用为 `assets/...`（与 app 和 learning-digest 的 `attachments/` 约定不一致），app 解析不到图片。已补 skill 硬性约定（附件一律放 `attachments/YYYY-MM-DD-HHMM/`、引用写相对仓库根路径）、把 4 张圆锥曲线截图 git mv 迁移到 `attachments/2026-08-03-2056/`、app 解析器兼容 `assets/` 前缀作为双保险（10 个单测全过）。

② **真机崩溃修复**：华为 ROM 没有 Java 10+ 的 `URLEncoder.encode(String, Charset)` 重载，导致 `NoSuchMethodError` 崩溃（"屡次停止运行"），已改为老重载 `encode(String, "UTF-8")`。真机验证通过：四个课题正常加载。

## 下一步动作

在真机上完成两项手动配置后真实使用：① 授予通知权限（设置→应用→学习召回→通知→允许）；② 华为电池优化白名单（设置→应用→学习召回→电池→启动管理→手动管理全开，否则 WorkManager 被杀通知不来）。之后真实使用几天，重点观察：18:30-19:30 无聊时段通知是否弹出、点通知是否直达详情页、「去做了」按钮计数（已埋点在 SharedPreferences 的 recall_done_count）。

## 悬而未决的问题

- v1 核心假设仍未验证：弹出旧问题（自己的原话）到底能不能把人钩回学习状态？——要等真机实测几天看「去做了」计数
- 华为 ROM 上 WorkManager 存活率未验证：即使加了白名单，通知能不能稳定到达还要观察
- `assets/` 兼容层是否保留？归档侧已统一为 `attachments/`，app 里双保险的 `assets/` 解析要不要留（倾向留，无害）
- 之前对话里贴过 fine-grained PAT，是否该 revoke 换新？

## 上下文

- 项目：`/Users/looboo/myprojects/learning_hook`，归档仓库 `diandianbao/learning_plan`
- 提交：`fbec319`（照片路径兼容）、归档侧 `a0b830a`（附件迁移+skill契约）、本地未提交：URLEncoder 修复
- 真机：华为 HBN-AL00（Android 10 时代运行库，模拟器 API 34 测不出 Java 10+ API 缺失问题）
- 教训：**写 Android 代码默认按老机型兼容 Java 标准库 API**，Java 8 之后的新 API 重载要避开，模拟器测不出，只能真机暴露
