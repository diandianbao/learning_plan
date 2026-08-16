---
topic: ComfyUI 视频生成
status: active
source: code-agent
created: 2026-08-16T15:29:19+0800
---

## 在做什么
在 Mac（Apple Silicon）上搭建 ComfyUI + MiniMax H3 本地视频生成流水线：从网络排查、模型验证、架构理解，到跑通第一个"hello world"（橘猫海滩视频）。已完成代理配置、官方 H3 t2v 工作流加载、comfy-kitchen MPS 补丁修复。

## 下一步动作
重启 Comfy Desktop（⌘Q 后重开）→ 重新 Queue 橘猫工作流（`~/ComfyUI-Shared/h3_t2v.json`），确认补丁后能生成出带声音的 mp4（若太慢：ResolutionSelector 缩放调小 / steps 20→12）

## 悬而未决的问题
- Mac MPS 上 int8 量化没有硬件加速（`torch._int_mm` 是 CUDA-only），生成速度慢（几十分钟量级），有没有更快的方案？（换 bf16 模型 61.7G 内存扛不扛得住？nvfp4/w4a8 版走模拟路径会不会更快？）
- comfy-kitchen 官方何时修复 issue #92？升级包后补丁会被覆盖，需要重新打
- "我既然已经有comfyui的desktop了，上面什么都没有，但是网页版打开之后上面却有一个流程图，它们俩是什么关系？"——Desktop 内置浏览器和外部浏览器的画布状态（localStorage）互不相通
- "latent英文本身是什么含义，用到comfyui里又是什么含义呢？"——已通过底片/潜影类比理解了，但 latent 的音频/视频联合结构（H3 一份 latent 同时含画面和声音）还想看代码层面怎么实现的

## 上下文
- ComfyUI 0.33.1 standalone（Comfy Desktop 启动），Python 3.13.12 + torch 2.12.1，64G 内存，MPS
- 模型在 `~/ComfyUI-Shared/models/`：minimax_h3_fl2va_pruned_int8_convrot（20G）+ qwen3vl_32b_minimax_h3_int8_convrot（25G）+ video/audio VAE（int8_convrot 是 ComfyUI 原生节点需要的格式）
- 工作流：官方 `video_minimax_h3_t2v.json`，已放 `~/ComfyUI-Shared/h3_t2v.json`；subgraph 结构：Load Diffusion Model / Load CLIP / Load VAE ×2 → MiniMaxH3ImageToVideo（t2va）→ Sampling（res_multistep, 20步）→ VAE 解码 → SaveVideo
- 网络：github.com 直连超时；`~/.zshrc` 已配代理（nc 检测 7890 + launchctl setenv 同步 GUI 程序）；git 无需单独配置
- 补丁：`comfy_kitchen/backends/eager/quantization.py` 的 `fast_int8_mm` 加非 CUDA 回退（fp32 matmul），备份 `quantization.py.bak_mps`，已用 MPS 张量验证零误差；对应官方 issue #92
- 首次运行报错：`aten::_int_mm not implemented for MPS`，发生在去噪第 0 步
