# 模组更新中心（Salt Player for Windows）

[![Release](https://img.shields.io/github/v/release/KylinZhangQAQ/SaltPlayer-Mod-Updater?label=release)](https://github.com/KylinZhangQAQ/SaltPlayer-Mod-Updater/releases/latest)
[![AI Generated](https://img.shields.io/badge/AI-100%25%20AI%20generated-blueviolet)](https://github.com/KylinZhangQAQ/SaltPlayer-Mod-Updater)
[![Microsoft Store](https://img.shields.io/badge/Salt%20Player-Microsoft%20Store-0078D4)](https://github.com/KylinZhangQAQ/SaltPlayer-Mod-Updater)
[![GitHub API](https://img.shields.io/badge/GitHub%20API-60%20req%2Fh-orange)](https://github.com/KylinZhangQAQ/SaltPlayer-Mod-Updater#-github-api-限制重要)

一个面向 **Salt Player for Windows（SPW）** 的本地插件管理工具。它会扫描 `workshop/plugins` 下的全部模组，读取每个模组清单里的 GitHub 仓库地址，检查 GitHub Releases，并提供带进度、错误码、失败原因和确认安装的一键更新流程。

> **AI 声明 / AI Disclosure**  
> 本项目的插件代码、界面、构建脚本和文档均由 AI 生成；仅使用 SPW Workshop API、PF4J 等第三方依赖。没有人工编写插件业务代码。

> **适用版本**  
> 本插件面向 **微软商店版（Microsoft Store）Salt Player for Windows**。实测环境为 **1.19.3**，Workshop API `0.1.0-dev22`。Steam 版的目录结构相同，但 Steam 创意工坊订阅的模组由 Steam 管理，本插件不调用 Steam 接口；在 Steam 版使用前请自行确认来源冲突。

> **⚠️ GitHub API 限制（重要）**  
> 未登录 GitHub 的匿名 API 限额只有 **60 次/小时**，批量检查、频繁重试或连续检查多个模组时很容易触发 `403 rate limit`。  
> 建议在 **设置 → GitHub Token** 中填入只勾选 `public_repo` 的 Token；Token 只保存在本机插件数据目录，不会上传。触发限制时界面会显示 `GH-403-RATE` 和恢复时间。

![更新中心界面](screenshots/ui-java.png)

## ⚠️ GitHub API 限制（重要）

- GitHub **未登录匿名 API 限额为 60 次/小时**；批量检查、连续重试或短时间内多次点击“检查全部”很容易触发限制。
- 触发后界面会显示 **`GH-403-RATE`**，并给出恢复时间；这时继续重试不会更快，反而会继续消耗/等待限额。
- 建议在插件 **设置 → GitHub Token** 中填入一个只勾选 `public_repo` 的 classic Token。Token 只保存在本机插件数据目录，不会上传到任何服务器。
- 一个 Token 可以显著提高 API 限额，适合一次检查多个模组；如果只是偶尔检查一两个模组，匿名访问通常够用。
- 下载 Release 资产本身不占用 GitHub API 的 60 次/小时限额，但获取 Release 元数据会占用。

## 功能

- **扫描全部已安装模组**：从 PF4J `MANIFEST.MF` 读取 `Plugin-Id`、`Plugin-Version`、`Plugin-Name`、`Plugin-Provider`、`Plugin-Open-Source-Url`。
- **GitHub Releases 检查**：读取最新正式 Release（可选包含 Pre-release），按语义化版本比较，自动选择 `.spmod` 资产。
- **缺少仓库地址时可手动补充**：在详情面板点“设置仓库”，支持 `https://github.com/作者/仓库`、`作者/仓库`、SSH 地址；映射保存在插件数据目录。
- **人类可读的下载进度**：进度条、已下载/总大小、实时速度、取消按钮。
- **明确的失败原因**：错误代码 + 中文原因 + 处理建议 + 可复制的技术详情。
- **下载后二次确认**：显示版本、安装包、大小、SHA-256，确认后才安装。
- **直接安装 + 关闭后安装兜底**：
  - 先尝试直接替换文件；
  - 如果文件被 Salt Player 占用，自动生成后台安装任务，等播放器退出后备份旧版、解压新版、失败自动回滚，并可自动重启。
- **安装前校验**：ZIP 完整性、路径穿越、插件 ID 与 Release 版本一致性、SHA-256（Release 提供 digest 时）。
- **自动检查**：启动时检查 / 每日检查 / 仅手动，三选一；默认启动时检查并提示。
- **网络适配**：可填 GitHub Token（提高 API 限额）、代理；代理留空时自动使用 Windows 系统代理，填 `direct` 强制直连。
- **零宿主权限**：不申请 `library-read`、`class-transform` 等权限，只在插件自己的数据目录和 `workshop/plugins` 内工作。

## 安装

1. 从 [Releases](https://github.com/KylinZhangQAQ/SaltPlayer-Mod-Updater/releases/latest) 下载 `plugin-dev.saltupdater-1.0.0.spmod`。
2. 打开 Salt Player → **设置 → 创意工坊 → 模组管理 → 导入模组**。
3. 选择 `.spmod`，确认名称后导入。
4. 在模组操作菜单选择 **启用**；本插件不申请权限，直接启用即可。
5. 打开该模组的 **配置**，点击 **打开模组更新中心**。

> 也可以直接把 `.spmod` 放在 `workshop/plugins` 下并解压为同名目录，再把 `dev.saltupdater` 写入 `enabled.txt`。推荐仍然使用官方的“导入模组”流程。

## 使用

1. 打开更新中心后，点 **检查全部**。
2. 左侧列表会显示每个模组的当前版本、最新版本和状态：
   - `可更新`：GitHub 上有更高版本；
   - `已是最新`：版本不高于当前；
   - `未设置仓库`：清单没有 `Plugin-Open-Source-Url`，点右侧“设置仓库”补充；
   - `失败`：右侧会显示错误代码、原因和建议。
3. 选中模组，右侧可以查看 Release 说明、打开仓库/发布页/安装目录。
4. 点 **更新**，确认下载；下载完成后会再次询问 **是否确认安装**。
5. 如果直接替换失败（文件被占用），会提示“已加入安装队列”。关闭 Salt Player 后，后台助手会：
   - 备份旧 `.spmod` 和旧目录；
   - 解压新版本；
   - 失败时回滚；
   - 如果开启了“关闭后自动重启”，自动启动 Salt Player。

### 批量更新

点 **全部更新**：先依次下载所有可更新模组，下载完成后统一确认安装。单个失败不会阻塞其他模组。

### 设置

配置页或更新中心右上角“设置”都可以调整：

| 设置 | 默认 | 说明 |
| --- | --- | --- |
| 自动检查 | 启动时检查 | 启动时检查 / 每日检查 / 仅手动 |
| 包含预发布版本 | 关 | 开启后会检查 Pre-release |
| 关闭后自动重启 | 开 | 仅对“关闭后安装”生效 |
| 启动时打开更新中心 | 关 | 启动完成后自动打开窗口 |
| 保留备份数 | 2 | 关闭后安装生成的备份数量 |
| GitHub Token | 空 | 可选；匿名 API 限额 60 次/小时 |
| 代理 | 空 | 留空自动使用 Windows 系统代理；填 `direct` 强制直连 |

## 错误代码

| 代码 | 含义 / 处理 |
| --- | --- |
| `NET-DNS` | 无法解析 GitHub 域名；检查 DNS 或代理 |
| `NET-CONNECT` | 无法连接 GitHub；检查网络/代理端口 |
| `NET-TIMEOUT` | 连接超时；稍后重试或换代理 |
| `NET-TLS` | TLS 握手失败；检查系统时间、证书替换软件、代理 HTTPS 支持 |
| `GH-401` | Token 无效或过期；重新生成 Token |
| `GH-403` | GitHub 拒绝请求；私有仓库需要 Token，或网络被限制 |
| `GH-403-RATE` | API 限额用完；填 Token 或等到提示的恢复时间 |
| `GH-404` | 仓库/Release 不存在；检查仓库地址、是否公开 |
| `GH-NO-RELEASE` | 没有可比较的 Release；确认作者发了 Release 且标签是版本号 |
| `GH-ASSET-MISSING` | Release 没有 `.spmod` 资产；打开发布页手动下载 |
| `REPO-INVALID` | 仓库地址格式无法识别 |
| `VER-INVALID` | 当前版本或标签不是可比较的版本号 |
| `DL-404` | 下载地址失效；重新检查或打开发布页 |
| `DL-403` | 下载被 GitHub 拒绝；私有仓库填 Token 或稍后重试 |
| `DL-5XX` | GitHub 下载服务器临时故障；稍后重试 |
| `DL-DISK` | 磁盘空间不足 |
| `DL-SIZE` | 下载大小与 Release 不一致 |
| `DL-HASH` | SHA-256 校验不匹配；重试并核对发布页 |
| `DL-CANCELLED` | 用户取消下载 |
| `PKG-ZIP` | ZIP 损坏或不完整 |
| `PKG-PATH` | 安装包包含路径穿越/绝对路径，已拒绝 |
| `PKG-ID-MISMATCH` | 包内插件 ID 与当前模组不一致，可能选错资产 |
| `PKG-VERSION-MISMATCH` | 包内版本与 Release 不一致 |
| `INS-LOCKED` | 文件被 Salt Player 占用；使用关闭后安装 |
| `INS-DEFER` | 无法创建后台安装任务；检查数据目录权限 |
| `INS-FAILED` | 直接安装失败；查看日志 |

日志位置：`workshop/data/dev.saltupdater/logs/updater.log`（在更新中心点“打开日志”即可）。

## 从源码构建

要求：

- JDK 25 或更高（构建脚本默认找 `JAVA_HOME`，其次 `D:\JDK26`）。
- 本仓库 `tools/` 下的编译依赖：
  - `spw-api.jar`（SPW Workshop API `0.1.0-dev22`，Apache-2.0）
  - `pf4j-3.12.0.jar`（Apache-2.0）

Windows：

```powershell
Set-ExecutionPolicy -Scope Process Bypass
.\build.ps1
```

产物：

```
build/libs/plugin-dev.saltupdater-1.0.0.spmod
```

`build.ps1` 只做三件事：`javac --release 25` 编译、复制资源、用 `tools/Build.java` 生成 `.spmod`。插件运行时只依赖 `java.base` + `java.desktop`，不打包任何第三方运行时库。

### 测试

```powershell
# 核心单元/集成测试：版本、JSON、仓库解析、扫描、包校验、直接安装
java -cp "build/test-classes;build/classes;tools/spw-api.jar;tools/pf4j-3.12.0.jar;tools/kotlin-stdlib-2.4.20.jar" SelfTest

# 真实 GitHub Release 探测（需要网络）
java -cp "build/test-classes;build/classes;tools/spw-api.jar;tools/pf4j-3.12.0.jar;tools/kotlin-stdlib-2.4.20.jar" LiveProbe

# 真实下载 + SHA-256 + 解压 + 直接安装
java -cp "build/test-classes;build/classes;tools/spw-api.jar;tools/pf4j-3.12.0.jar;tools/kotlin-stdlib-2.4.20.jar" LiveDownloadTest

# 关闭后安装 PowerShell 助手
java -cp "build/test-classes;build/classes;tools/spw-api.jar;tools/pf4j-3.12.0.jar;tools/kotlin-stdlib-2.4.20.jar" DeferredTest
```

## 目录结构

```
src/main/java/dev/saltupdater/
  MainPlugin.java             插件入口、生命周期、自动检查
  ConfigActions.java          配置页按钮入口
  core/
    AppPaths.java             逻辑/物理 workshop 路径（MSIX 兼容）
    ModScanner.java           扫描模组清单与 .spmod
    Version.java              宽松语义化版本比较
    GitHubClient.java         GitHub Releases / 资产选择 / 错误映射
    Downloader.java           流式下载、进度、取消、SHA-256
    PackageValidator.java     ZIP 安全校验与解压
    InstallService.java       直接安装、备份、回滚、关闭后安装
    UpdateService.java        检查/下载/安装编排
    ProxySupport.java         设置代理 + Windows 系统代理自动检测
    Json.java                 无第三方依赖的 JSON 读写
  ui/
    UpdateCenterWindow.java   原生 Swing 更新中心
```

## 安全与隐私

- 插件在 Salt Player 进程内运行；Workshop 权限机制不是 OS 沙箱。本插件不申请任何宿主权限。
- 只读取 `workshop/plugins` 下的清单和 `.spmod` 元数据；只写入插件自己的数据目录和用户在界面中确认的模组文件。
- 网络请求只发给 `api.github.com`、`github.com` 和 GitHub Release 资产域名；不会上传本地曲库、模组列表或 Token。
- Token 只保存在本机 `workshop/data/dev.saltupdater/config.json`，请勿把该文件分享给他人。
- 安装前会校验 ZIP 路径、插件 ID、版本和可用的 SHA-256；最终安装仍需用户确认。
- “关闭后安装”使用插件数据目录中的 PowerShell 脚本，只处理该目录下的任务文件；脚本会等待 Salt Player 退出，失败自动回滚。

## 已知限制

- 只支持 GitHub Releases 中的 `.spmod` 资产；没有 `.spmod` 的 Release 会提示手动下载。暂不支持 `.zip` / 源码包。
- GitHub 标签需要是可比较的版本号（`v1.2.3`、`1.2.3.4` 等）；纯日期标签也可以，但 `nightly` 这类标签无法比较。
- Steam 创意工坊订阅的模组由 Steam 更新；本插件不会调用 Steam 接口。对 Steam 模组执行本地安装可能产生来源冲突，请自行确认。
- 直接替换文件时，已经加载的插件类不会在运行中的 JVM 里重新加载；如果播放器仍显示旧版本，重启后生效。文件被占用时会自动转为关闭后安装。
- 匿名 GitHub API 限额为 60 次/小时；批量检查较多模组时建议填写 Token。
- 本插件自身的仓库是 <https://github.com/KylinZhangQAQ/SaltPlayer-Mod-Updater>，因此它也可以参与自更新；但正在运行的插件文件可能被占用，更新自身时通常会走“关闭后安装”。

## 在插件库中找到本插件

本仓库已添加 GitHub topic **`salt-player-plugins`**。在 Salt Player 的 **模组管理 → 寻找模组** 中，应可通过该 topic 发现本仓库；如果列表有缓存，请稍后重试或重启播放器。

也可以直接打开 GitHub topic 页面查看：<https://github.com/topics/salt-player-plugins>

## 许可

本插件代码使用 MIT License。SPW Workshop API 与 PF4J 的许可信息见 `tools/` 中对应依赖及上游仓库。
