# 云盘批量重命名（移动云盘适配版）

基于 [a1mersnow/drive-rename](https://github.com/a1mersnow/drive-rename)（GPL）二次调整的本地增强版，保留原脚本全部网盘能力，重点重写了移动云盘 provider，并新增一套 ReNamer 风格的重命名规则链面板。

本仓库用于**留存各版本脚本文件**，便于安装、对比与回滚。

## 特性

- **移动云盘（yun.139.com）2026 接口适配**：按页面实际请求体计算 `mcloud-sign`，补全必需请求头；列表走 `POST /hcy/file/list`（`nextPageCursor` 分页），改名走 `POST /hcy/file/update`，中文名可用。
- **重命名方案（规则链）**：12 种规则可顺序叠加——重构文件名 / 修改大小写 / 移动字符 / 删除字符 / 按模式删除字符 / 重新编号 / 替换字符 / 添加 / 按列表重命名 / 按列表替换字符 / 文件名内按分隔符互换内容 / 从文件名两端修剪。支持读取目录 → 预览原名与新名 → 勾选执行，同批重名标红，方案存 `localStorage`。
- **选取前 N 条**：只处理前 N 条可执行项，回车应用，数值持久化。
- **执行中可停止**：当前一条完成后不再继续，未处理条目保持勾选，可续跑。
- 其他网盘（115 / 123 / 阿里 / 百度 / 天翼 / 夸克 / UC）沿用原脚本逻辑。

## 安装

1. 打开 `latest/drive-rename.user.js`，在 GitHub 页面点 **Raw**，Tampermonkey 会提示安装/更新。
2. 访问 `https://yun.139.com/w/`，页面右下角出现「重命名方案」胶囊按钮。

> 本仓库为 **private** 时，Raw 链接需要登录 GitHub，Tampermonkey 无法自动检查更新，只能手动下载覆盖安装。若需要脚本自动更新，请把仓库可见性改为 public，并把脚本头部的 `@downloadURL` / `@updateURL` 指向 `latest/drive-rename.user.js` 的 raw 地址。

脚本标识保持与原版一致（`@name 云盘批量重命名` + `@namespace vite-plugin-monkey`），因此可直接覆盖更新，不会变成第二个脚本。

## 目录结构

```
latest/drive-rename.user.js     最新版（稳定文件名，便于固定安装链接）
history/                        历史版本，文件名带版本号
manifest.json                   版本清单：版本号 / 大小 / blob sha
publish-to-github.ps1           本地发布脚本（每次运行会同步备份到 tools/）
```

## 版本

| 版本 | 主要改动 |
|---|---|
| v1.6.3 | 新增「停止」按钮：执行中可中断，当前一条完成后不再继续，未处理条目保持勾选可续跑 |
| v1.6.2 | 新增「选取前 N 条」数量控制，回车应用，数值记忆 |
| v1.6.1 | 更新 `@description` 与备注（更新日志），功能无变化 |
| v1.6 | 新增「重命名方案」规则链面板（12 种规则、顺序叠加、预览、勾选执行、持久化） |
| v1.5 | 重写移动云盘 provider：`mcloud-sign` 签名、请求头补全、列表与改名接口接入、修复版本号比较误报 |
| v1.4.9 | 原作者 a1mersnow 的原始发布版，作为回滚底稿 |

> v1.5 / v1.6 / v1.6.1 为按代码演进逆推的**重建版**（原始文件曾被覆盖删除），字节数与原文件一致（158,652 / 189,318 / 191,617 B），文件名带「重建版」标记。

## 发布方式

本仓库由 `publish-to-github.ps1` 维护：

1. 生成 fine-grained PAT（仅勾选本仓库的 Contents: Read and write）。
2. `setx GH_TOKEN "github_pat_xxx"`，重开终端。
3. 在本地脚本目录运行 `pwsh -File .\publish-to-github.ps1`，自动推送最新版到 `latest/`、历史版本到 `history/`，并刷新 `manifest.json`。

脚本幂等：内容未变化（blob sha 相同）时跳过，不会产生空提交。

## 致谢与许可

原作者 [a1mersnow](https://github.com/a1mersnow/drive-rename)，GPL 许可。本仓库的改动仅涉及移动云盘适配与方案面板，未变更原脚本标识与许可。