# 缘之空 krkrZ 重置项目总览

## 项目根目录

项目路径：

`C:\Users\misakamiro\Downloads\Compressed\yosuga_krkrZ\yosuga_krkrZ`

本文档只负责项目管理、模块边界、交接规范和进度记录。项目总控文档负责人不得修改游戏代码、脚本、图片、音频、视频、字体、dll、exe、存档或现有资源文件。

## 项目目标

1. 将现有 krkr2 项目迁移并修复到 krkrZ 可运行状态。
2. 将游戏核心显示目标提升到 `1920x1080`。
3. 对背景、CG、立绘、UI 图执行 16:9 扩展和高清化，工具范围包括 NovelAI、waifu2x、Photoshop。
4. 重做现代化 UI。
5. 重做 UI 动画。
6. 优化启动、脚本解析、资源加载、界面切换和运行时性能。
7. 打包成用户可安装的 Windows exe 安装包。

## 当前目录扫描结果

扫描时间：2026-05-28

根目录关键文件：

- `startup.tjs`：当前根启动脚本，已注册 `system/` 和 `content-data/` 自动搜索路径，并加载 `k2compat.tjs`、`Status.tjs`、`Initialize.tjs`、`begin.tjs`。
- `k2compat.tjs`：根兼容层入口。
- `tvpwin32.exe`：当前 krkrZ 运行入口。
- `tvpwin32.cf`、`ヨスガノソラ.cf`：配置文件。
- `KAGParser.dll`、`KAGParserEx.dll`、`csvParser.dll`、`windowEx.dll`、`layerExDraw.dll`、`krmovie.dll`、`menu.dll` 等：运行依赖 dll。
- `bgData.csv`：背景索引数据。

顶层目录和用途：

| 目录 | 当前文件量 | 主要类型 | 用途 |
| --- | ---: | --- | --- |
| `bg\` | 117 | `.tlg`, `.png` | 背景图和少量转换样张 |
| `bgm\` | 41 | `.ogg`, `.sli` | BGM 与循环点 |
| `bg_output\` | 1 | `.png` | 背景扩图或导出结果的临时/输出目录 |
| `content-data\` | 22430 | `.ogg`, `.tlg`, `.ks`, `.ctx`, `.tjs` | 从原封包或整合数据中展开的大量内容资产与脚本副本 |
| `font\` | 30 | `.tft` | 字体 |
| `frame\` | 197 | `.tlg` | UI 框、按钮、窗口相关图 |
| `icon\` | 1 | `.ico` | 程序图标 |
| `k2compat\` | 9 | `.tjs` | krkr2 兼容辅助脚本 |
| `rule\` | 57 | `.png` | 转场 rule 图 |
| `savedata\` | 7 | `.bmp`, `.dat`, `.log` | 本地存档和运行痕迹 |
| `scenario\` | 306 | `.ks` | 主剧本脚本 |
| `se\` | 120 | `.ogg` | 音效 |
| `system\` | 65 | `.tjs`, `.csv`, `.ks`, `.bak`, `.corrupted` | 系统脚本、UI、消息框、选择项、配置、标题和兼容层 |
| `thumb\` | 951 | `.tlg` | CG/回想缩略图 |

主要扩展名规模：

- `.ogg`：17839 个，声音资源集中在 `content-data\`、`bgm\`、`se\`。
- `.tlg`：4816 个，图像资源集中在 `content-data\`、`bg\`、`frame\`、`thumb\`。
- `.ks`：615 个，剧本和 KAG 脚本集中在 `scenario\`、`content-data\`。
- `.ctx`：307 个，剧本或文本相关数据集中在 `content-data\`。
- `.tjs`：297 个，系统脚本集中在 `system\`、`k2compat\`、`content-data\system*`。
- `.csv`：150 个，背景、角色、系统索引数据。
- `.png`：116 个，当前高清化/导出图、转场图、少量背景样张。

## 模块列表

| 模块 | 核心目标 | 主负责人 |
| --- | --- | --- |
| `engine-compat` | krkr2 到 krkrZ 兼容层、启动链路、dll/API 差异修复 | 凝言 |
| `scenario-parser` | 剧本解析、选项、跳转、回想和分支验证 | yuuki |
| `resolution-1920x1080` | 全局 1920x1080 坐标、窗口、消息层、背景层适配 | 盐甘草糖块 |
| `ui-redesign` | 标题、配置、存读档、CG、消息框、按钮等现代化 UI | 凝言、盐甘草糖块 |
| `ui-animation` | UI 入场、退场、按钮反馈、转场动画 | yuuki、凝言 |
| `image-remaster` | 背景、CG、立绘、UI 图扩图、放大、补边和格式回灌 | 虹映千夏、03r、凝言 |
| `image-qa` | 图像人工质检、错图标注、批次验收 | himahima、happy |
| `performance` | 启动、资源加载、场景切换、动画和内存性能 | 盐甘草糖块、yuuki |
| `packaging` | 用户可安装 exe、依赖收集、安装/卸载、版本发布 | happy、凝言 |

## 工作原则

1. 任何人只修改自己模块拥有的文件范围。
2. 跨模块文件必须先写交接单，再由对应模块负责人确认。
3. 不直接覆盖原始资源。图像、UI、脚本的新增版本必须有批次目录、来源记录和可回退方案。
4. `savedata\` 不进入交付包，除非 packaging 模块明确需要测试样例。
5. `.corrupted`、`.bak` 文件只作为排查参考，不作为最终交付文件直接启用。
6. 每次提交或交接前必须填写工作日志，说明改了哪些路径、如何测试、剩余风险。

## 文档索引

- `docs\project\00-project-overview.md`：项目目标、目录扫描结果、模块总览。
- `docs\project\01-file-module-split.md`：每个模块的负责人员、负责文件、禁止修改范围、输入依赖、输出交付物、验收标准、时间计划。
- `docs\project\02-personnel-task-plan.md`：按人员拆分任务、协作边界和交接要求。
- `docs\project\03-schedule.md`：阶段计划、里程碑、依赖关系和验收节奏。
- `docs\project\04-handoff-template.md`：执行人员交接模板。
- `docs\project\05-progress-report-template.md`：每日/每批次进度报告模板。
