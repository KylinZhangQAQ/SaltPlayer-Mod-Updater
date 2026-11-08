# v1.0.0 — 模组更新中心（完全由 AI 制作）

> **AI 声明 / AI Disclosure**  
> 本项目的插件代码、界面、构建脚本和文档 **完全由 AI 生成**；仅使用 SPW Workshop API、PF4J 等第三方依赖。没有人工编写插件业务代码。

> **适用版本**  
> 本版本面向 **微软商店版（Microsoft Store）Salt Player for Windows**。实测环境为 **SPW 1.19.3**、Workshop API `0.1.0-dev22`。Steam 版目录结构相同，但 Steam 创意工坊订阅的模组由 Steam 管理，本插件不调用 Steam 接口。

> **⚠️ GitHub API 限制（重要）**  
> GitHub 匿名 API 限额只有 **60 次/小时**。批量检查、频繁重试或连续检查多个模组时很容易触发 `403 rate limit`。  
> 建议在插件 **设置 → GitHub Token** 中填入只勾选 `public_repo` 的 Token；Token 只保存在本机插件数据目录，不会上传。触发限制时界面会显示 `GH-403-RATE` 和恢复时间。

## 功能

- 扫描 `workshop/plugins` 下全部模组，读取 PF4J `MANIFEST.MF` 中的 GitHub 仓库地址。
- 读取 GitHub Releases，按版本号比较，自动选择 `.spmod` 资产。
- 缺少 `Plugin-Open-Source-Url` 时，可在界面中手动补充仓库地址，并按插件 ID 记住。
- 下载进度条、已下载/总大小、实时速度、取消按钮。
- 下载完成后二次确认安装，显示版本、包名、大小、SHA-256。
- 失败时显示错误代码、原因、建议和可复制的技术详情（例如 `NET-DNS`、`GH-403-RATE`、`GH-ASSET-MISSING`、`DL-HASH`、`PKG-ID-MISMATCH`、`INS-LOCKED`）。
- 先尝试直接替换；文件被占用时自动转为“关闭 Salt Player 后安装”，备份旧版、失败回滚，并可自动重启。
- 自动检查：启动时检查 / 每日检查 / 仅手动，默认启动时检查。
- **零宿主权限**：不申请 `library-read` 或 `class-transform`。

## 安装

1. 下载本 Release 中的 `plugin-dev.saltupdater-1.0.0.spmod`。
2. Salt Player → **设置 → 创意工坊 → 模组管理 → 导入模组**。
3. 选择 `.spmod`，确认后启用。
4. 打开该模组的 **配置 → 打开模组更新中心**。

## 校验

```text
SHA-256: ed5cc6fad7c0a03cf60f35686e5f8a5131f5a7c9d7e9cd3f8dc7c6fffb15ae6c
```

## 已知限制

- 只支持 GitHub Releases 中的 `.spmod` 资产；没有 `.spmod` 的 Release 会提示手动下载。
- 标签需要是 `v1.2.3`、`1.2.3.4` 这类可比较版本号；`nightly` 这类标签无法比较。
- 直接替换时，已加载的旧插件类不会在运行中的 JVM 里重新加载；如果播放器仍显示旧版本，重启后生效。
- Steam 创意工坊订阅的模组由 Steam 更新，本插件不处理 Steam 订阅。

## 在插件库中找到本插件

本仓库已添加 GitHub topic **`salt-player-plugins`**。在 Salt Player 的 **模组管理 → 寻找模组** 中，应可通过该 topic 发现本仓库。如果列表有缓存，请稍后重试或重启播放器。
