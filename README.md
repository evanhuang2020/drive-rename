# drive-rename · 云盘批量重命名（个人增强版）

基于 [a1mersnow/drive-rename](https://github.com/a1mersnow/drive-rename) 的个人自用增强版 userscript，主要做了 **移动云盘（yun.139.com）2026 版接口适配**，并新增了「重命名方案」规则链面板、选区数量控制与执行中断能力。

- 当前版本：**1.6.8**
- 更新通道：本仓库 `main` 分支（Tampermonkey 自动更新已指向这里）
- 仓库状态：Public（公开，raw 链接可直接被 Tampermonkey 拉取）

## 安装 / 更新

直接安装（Tampermonkey / 脚本猫均可）：

```
https://raw.githubusercontent.com/evanhuang2020/drive-rename/main/latest/drive-rename.user.js
```

脚本头部已写入：

| 字段 | 值 |
| --- | --- |
| `@downloadURL` / `@updateURL` | `https://raw.githubusercontent.com/evanhuang2020/drive-rename/main/latest/drive-rename.user.js` |
| `@homepageURL` | `https://github.com/evanhuang2020/drive-rename` |

发新版本时只需覆盖 `latest/drive-rename.user.js`，版本号递增后 Tampermonkey 会自动提示更新。

## 目录结构

```
drive-rename/
├─ latest/drive-rename.user.js         # 最新版脚本（Tampermonkey 自动更新读取这里）
├─ history/                            # 历史版本（含原始 1.4.9 发布版）
├─ manifest.json                       # 各版本清单（版本号 / 字节数 / git blob sha）
└─ README.md
```

## 版本清单

| 版本 | 字节数 | 说明 |
| --- | --- | --- |
| 1.6.8 | 212,809 | 重命名全部完成后 3 秒自动刷新页面（可取消）；面板/浮窗整体高度可拖标题栏自由调整并记忆；浮窗与预览区默认按内容自适应，不再留大片空白 |
| 1.6.7 | 208,317 | 规则变多后预览被挤掉：面板改三段式（固定工具栏 / 规则区独立滚动 / 预览区常驻可折叠可拖高）|
| 1.6.6 | 202,779 | 合并改为双模式：找到文件列表窗口则合并，找不到自动回落独立浮窗 + 胶囊入口（规则功能不会再丢失）；标题栏显示当前模式 |
| 1.6.5 | 199,047 | UI 合并：规则链面板并入文件列表窗口，上方文件列表 / 下方规则链（可折叠） |
| 1.6.4 | 196,477 | 更新源切换到本仓库（`@downloadURL`/`@updateURL`/面板检查更新均指向 raw），功能同 1.6.3 |
| 1.6.3 | 195,946 | 新增「停止」按钮，执行重命名过程中可中断，未处理条目保持勾选可续跑 |
| 1.6.2 | 194,151 | 新增「选取前 N 条」数量控制（留空/0 为全部，值持久化） |
| 1.6.1 | 191,617 | 规则链面板交互细节修正（重建版，字节数与原始一致） |
| 1.6.0 | 189,318 | 内置「重命名方案」规则链面板（12 种规则可顺序叠加、预览、勾选执行） |
| 1.5.0 | 158,652 | 移动云盘 2026 接口适配（新版签名 `mcloud-sign`、必需请求头、`/hcy/file/update` 改名） |
| 1.4.9 | 155,116 | 上游原始发布版（来源 GreasyFork 479295） |

精确的字节数与 git blob sha 见 [`manifest.json`](manifest.json)。说明：1.5 / 1.6 / 1.6.1 三个版本当时未记录哈希，是按功能逐层回退重建的，文件名带「重建版」标记，功能特征已逐项核对。

## 本地增强内容（相对上游 1.4.9）

1. **移动云盘适配**：`sign = md5(md5(base64(排序后 encodeURIComponent(JSON.stringify(body)))) + md5(ts+":"+rand)).toUpperCase()`，请求头补齐 `x-yun-client-info`、`X-Deviceinfo` 等必需项；改名走 `POST https://personal-kd-njs.yun.139.com/hcy/file/update`，body `{fileId, name, description:""}`。
2. **重命名方案规则链面板**：12 种规则（重构文件名 / 修改大小写 / 移动字符 / 删除字符 / 按模式删除字符 / 重新编号 / 替换字符 / 添加 / 按列表重命名 / 按列表替换字符 / 文件名内按分隔符互换内容 / 从文件名两端修剪），可上移下移删除启用、顺序叠加、实时预览、勾选执行、冲突标红，规则持久化在 `localStorage: dr.rules.v1`。
3. **数量控制**：工具栏「选取前 [N] 条 应用」，按当前排序勾选前 N 条可执行项。
4. **停止按钮**：执行中可中断，未处理条目保持勾选，可再点执行继续。
5. **界面合并（1.6.5 ~ 1.6.7）**：规则链区并入原脚本右侧的文件列表/预览窗口，形成单一窗口 —— 上部分是文件列表与预览（原样保留、独立滚动），下部分是规则链工具栏与规则卡（带「收起」可整块折叠）；配色统一为浅色主题。**1.6.6 起为双模式**：若脚本找不到原文件列表窗口（不同网盘/页面结构不同），规则区会自动回落到右下角的独立浮窗，并保留「重命名方案」胶囊入口，功能不会消失；标题栏右侧会显示「已合并 / 独立浮窗」。路由切换或面板重建时会自动重新挂载。**1.6.7 起面板内部为三段式**：标题栏与工具栏（读取当前目录 / 选取前 N 条 / 全选 / 执行 / 停止 / 添加方案）固定不动，规则卡在中间独立滚动，改名预览常驻在面板底部（默认约 20vh，可点「收起/展开」折叠，也可上下拖动预览标题栏调高，高度记在 `localStorage: dr.pv.h`），再加多条方案也不会把预览顶出屏幕。

## 与上游的关系

- 脚本标识（`@name` / `@namespace` / `@author` / `@license`）保持与上游一致，避免 Tampermonkey 识别成两个脚本。
- 上游仍在 GreasyFork 与 a1mersnow/drive-rename 发布，本仓库只作为**个人自用分支**，不替代上游。
- 原脚本许可：GPL。
