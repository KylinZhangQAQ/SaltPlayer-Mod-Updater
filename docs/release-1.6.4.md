# 模组更新中心 1.6.4

发布日期：2026-11-16

1.6.4 修复另一台电脑上插件更新后无法自动重启的问题。

## 修复

- 后台安装改用 Windows `tar.exe` 解压，支持深目录插件包，避免 MAX_PATH 解压失败。
- 自动重启优先使用固定 AppId 的 `shell:AppsFolder` 入口，不再依赖 `Get-AppxPackage`。
- 多个关闭后安装任务只在全部完成后重启一次。
- 安装进行中阻止重新打开旧状态页面。
- 安装成功清理下载包，失败保留缓存。

## 安装

下载 `plugin-dev.saltupdater-1.6.4.spmod`，在 Salt Player 的模组管理中导入并启用。
