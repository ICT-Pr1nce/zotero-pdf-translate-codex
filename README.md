# Translate for Zotero（Codex 增强版）试用说明

这是基于 [windingwind/zotero-pdf-translate](https://github.com/windingwind/zotero-pdf-translate) `2.4.6` 修改的个人测试版。它保留原插件的划词自动翻译、阅读器弹窗、侧栏、批注、笔记和原有翻译服务，只增加本文列出的功能。

安装包名称：`translate-for-zotero-codex.xpi`

> 当前版本：`2.4.6-codex.2`。这是试用版本，不提供自动更新，主要在 Windows 上测试。修改日期：`2026-07-14`。

## 这版改了什么

- 新增 **Codex（ChatGPT 登录）** 翻译服务：使用本机 Codex CLI 已保存的 ChatGPT 登录，不需要填写 OpenAI API Key。
- Codex 可配置模型预设、自定义模型、推理强度、超时、`codex.exe` 路径和额外翻译要求。
- 请求时才启动 Codex 子进程，翻译完成后立即退出，不需要常驻后台服务。
- Codex 请求固定走 HTTP/SSE，避免 WebSocket 重连造成长时间卡顿；临时参数不会修改个人的 Codex 全局配置。
- 增加整个 Zotero 会话内的内存缓存：相同服务、原文和翻译方向再次出现时直接复用结果，退出 Zotero 后自动清空。
- 翻译弹窗和侧栏显示耗时：`⏱` 表示实际请求耗时，`⚡` 表示命中会话缓存。

## 安装

### 1. 安装插件

1. 打开 Zotero 的“工具 → 插件”。
2. 点击右上角齿轮，选择“从文件安装插件”。
3. 选择 `translate-for-zotero-codex.xpi`，然后重启 Zotero。

本版沿用原版 Translate for Zotero 的插件 ID，所以会**直接替换原版**并继续使用原有设置，不需要让两个版本共存。

兼容范围为 Zotero `7.9.9` 至 `10.9.9`。如果以后重新安装官方版 Translate for Zotero，Codex、会话缓存和耗时显示等增强功能会随之消失。

### 2. 只在使用 Codex 时：安装并登录 Codex CLI

如果只使用 DeepSeek、Google、DeepL 等原有服务，可以跳过本节。

Windows 推荐安装官方独立版：

```powershell
powershell -ExecutionPolicy Bypass -c "irm https://chatgpt.com/codex/install.ps1 | iex"
```

安装后打开 PowerShell，完成一次 ChatGPT 登录并检查状态：

```powershell
codex login
codex login status
```

官方说明见 [Codex CLI](https://developers.openai.com/codex/cli) 和 [Codex 登录](https://developers.openai.com/codex/auth)。不要把 `~/.codex/auth.json` 发给任何人，它包含敏感登录凭据。

Windows 独立版通常位于：

```text
%LOCALAPPDATA%\Programs\OpenAI\Codex\bin\codex.exe
```

插件会优先自动查找这个位置。自动查找失败时，可在 Codex 服务配置里填写完整路径。

## 配置与使用

1. 打开“Zotero 设置 → 翻译”。
2. 在“翻译服务”中选择 **Codex（ChatGPT 登录）**。
3. 点击服务旁的“配置”，按需修改下列项目并保存：
   - **模型预设**：日常翻译推荐 `GPT-5.6 Terra — 均衡`。
   - **推理强度**：日常翻译推荐 `无`；更高强度通常会明显变慢。
   - **超时**：默认 `180` 秒。
   - **Codex 可执行文件**：通常留空自动查找。
   - **附加翻译要求**：可填写学科领域、固定术语或文风要求。
4. 按原插件习惯使用即可：在 PDF、EPUB 或网页阅读器中选中文本，插件会自动显示翻译弹窗，无需额外点击“翻译”。

模型是否可用由当前 ChatGPT 账号和 Codex 版本决定。某个预设不可用时，可以更新 Codex CLI，或选择“自定义模型”填写账号实际可用的模型名称。

## 缓存和耗时说明

缓存对本次 Zotero 运行期间的翻译任务生效。相同服务、相同原文、相同源语言和目标语言会复用结果；Codex 的模型、推理强度或附加要求变化后会重新翻译。缓存只保存在内存中，不会写入文献库，完全退出 Zotero 后会清空。

耗时从插件开始处理任务时计算，包含本地进程启动、网络请求和生成时间。第一次请求通常较慢，命中缓存时通常接近瞬时完成。

## 常见问题

### 提示找不到 Codex

先在 PowerShell 运行：

```powershell
codex --version
codex login status
```

如果命令正常，但插件仍找不到，请在 Codex 服务配置中填写 `codex.exe` 的完整路径。优先使用官方独立版，不要填写 Windows Store 的受保护应用路径。

### Codex 翻译很慢或超时

先选择 `GPT-5.4 mini` 和“推理强度：无”。Codex 的速度仍会受到网络、账号用量和服务器负载影响；超时只会终止本次请求，不会让 Codex 常驻后台。若持续失败，先运行 `codex login status`，必要时重新执行 `codex login`。



## 致谢与许可

本项目基于 windingwind 的 Translate for Zotero 修改，原项目采用 `AGPL-3.0-or-later` 许可证。本测试版保留相同许可证；原插件的完整介绍和使用文档请查看[上游项目](https://github.com/windingwind/zotero-pdf-translate)。
