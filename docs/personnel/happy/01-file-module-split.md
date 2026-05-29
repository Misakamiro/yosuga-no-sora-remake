# 文件与模块拆分

项目根目录：

`C:\Users\misakamiro\Downloads\Compressed\yosuga_krkrZ\yosuga_krkrZ`

本文档用于新人认领任务。所有路径均相对项目根目录书写，执行时必须先确认完整路径。

## 总边界

允许项目总控文档负责人维护：

- `docs\project\*.md`

禁止项目总控文档负责人修改：

- `startup.tjs`
- `k2compat.tjs`
- `system\*.tjs`
- `scenario\*.ks`
- `bg\*`
- `frame\*`
- `thumb\*`
- `content-data\*`
- `bgm\*`
- `se\*`
- `font\*`
- `*.dll`
- `*.exe`
- `*.cf`
- `savedata\*`

执行人员如需跨模块修改，必须在交接单中写明原因、涉及路径、预期影响、回滚方式。

---

## 引擎兼容模块（engine-compat）

### 负责人员

- 主负责人：凝言
- 协助：yuuki

### 负责文件

- `startup.tjs`
- `k2compat.tjs`
- `k2compat\*.tjs`
- `system\KAGParserCompat.tjs`
- `system\csvParserCompat.tjs`
- `system\GdiPlusCompat.tjs`
- `system\win32dialog.tjs`
- `system\k2compat_*.tjs`
- `system\Initialize.tjs`
- `system\Status.tjs`
- `system\System.tjs`
- `system\SystemWindow.tjs`
- 根目录运行依赖：`tvpwin32.exe`、`*.dll`、`*.cf`

### 禁止修改范围

- 不直接改 `scenario\*.ks` 的剧情内容。
- 不改 `bg\`、`frame\`、`thumb\`、`content-data\*.tlg`、`content-data\*.ogg` 资源内容。
- 不删除 `.corrupted`、`.bak` 参考文件。
- 不改 `savedata\`，除非为了复现兼容问题复制到单独测试目录。

### 输入依赖

- krkrZ 启动日志。
- `startup.tjs` 当前启动链路。
- `system\*.corrupted` 与 `.bak` 的对照信息。
- scenario-parser 模块提供的脚本解析失败样例。
- resolution 模块提供的窗口和坐标需求。

### 输出交付物

- 兼容层修改说明。
- 启动链路说明：从 `startup.tjs` 到 `begin.tjs` 的加载顺序。
- krkr2 API 替换清单。
- 已知不兼容行为清单。
- 最小启动验证记录。

### 验收标准

- 双击或命令行启动 `tvpwin32.exe` 后不因缺失脚本、缺失类、缺失 dll 直接崩溃。
- `startup.tjs` 能完成 `k2compat.tjs`、`Status.tjs`、`Initialize.tjs`、`begin.tjs` 加载。
- 常用窗口、对话框、字体选择、输入框兼容脚本不会弹出阻塞式错误。
- 与 `scenario-parser`、`resolution-1920x1080` 的接口说明完整。

### 时间计划

- 第 1 周：整理启动链路和兼容 API 清单。
- 第 2 周：修复启动和系统窗口兼容问题。
- 第 3 周：配合剧本解析和 1080p 改造修复联动问题。
- 第 4 周：冻结兼容层接口，只接受阻塞级 bug。

---

## 剧本解析模块（scenario-parser）

### 负责人员

- 主负责人：yuuki
- 协助：盐甘草糖块

### 负责文件

- `scenario\*.ks`
- `content-data\*.ks`
- `content-data\*.ctx`
- `system\KAGParserCompat.tjs`
- `system\ScController.tjs`
- `system\SelectItem.tjs`
- `system\CgFlag.tjs`
- `system\CgModeList.tjs`
- `system\CgSetupInfo.tjs`
- `system\movie.ks`

### 禁止修改范围

- 不改 UI 视觉图：`frame\*`、`rule\*`、`thumb\*`。
- 不改声音资源：`bgm\*`、`se\*`、`content-data\*.ogg`。
- 不直接改引擎 dll/exe。
- 不为了跳过错误删除剧情、选项或标签。

### 输入依赖

- engine-compat 提供的 KAG 兼容行为。
- resolution 模块提供的消息区、选项区坐标。
- image-remaster 提供的新图像命名和回灌路径。
- QA 提供的断点、坏跳转、错误选项复现记录。

### 输出交付物

- 剧本文件清单：按 `00_a*.ks`、`00_b*.ks`、`00_c*.ks` 等路线分组。
- 选项和跳转索引表。
- 解析错误清单和修复说明。
- 至少一条从标题进入正文、出现选项、跳转分支、返回存读档的验证记录。

### 验收标准

- `scenario\` 下 306 个 `.ks` 文件能被解析器顺序扫描，标签、跳转、选项引用无明显缺失。
- 选项显示、选择、跳转、回退和存档点不破坏原剧情结构。
- CG 解锁、回想、电影脚本引用能够和 `system\Cg*.tjs` 对上。
- 对新增或修改的剧本解析规则有明确测试样例。

### 时间计划

- 第 1 周：建立 `.ks`、`.ctx` 清单和路线分组。
- 第 2 周：修复解析、标签、选项跳转问题。
- 第 3 周：联调 CG、回想、存读档和 1080p 选项位置。
- 第 4 周：锁定解析规则，转入回归验证。

---

## 1920x1080 分辨率模块（resolution-1920x1080）

### 负责人员

- 主负责人：盐甘草糖块
- 协助：凝言

### 负责文件

- `system\Window.tjs`
- `system\SystemWindow.tjs`
- `system\ADVScreen.tjs`
- `system\ADVObject.tjs`
- `system\MessageArea.tjs`
- `system\MessageFrame.tjs`
- `system\SetupADVObject.tjs`
- `system\SelectItem.tjs`
- `system\ConfigWindow.tjs`
- `system\Album.tjs`
- `system\Title.tjs`
- `system\bgData.csv`
- 根目录 `bgData.csv`
- 配置文件：`tvpwin32.cf`、`ヨスガノソラ.cf`

### 禁止修改范围

- 不重绘 UI 图，不直接产出最终视觉资源。
- 不改变剧情脚本语义。
- 不替换运行时 dll/exe。
- 不把临时比例缩放方案当最终 1080p 适配方案。

### 输入依赖

- 原始资源尺寸和图像模块的新资源尺寸。
- UI-redesign 提供的页面布局稿。
- engine-compat 提供的窗口创建和层 API 行为。
- scenario-parser 提供的消息框、选项、名称栏显示需求。

### 输出交付物

- 1920x1080 全局坐标规范。
- 背景、立绘、消息框、选项、系统菜单、CG 模式页面的坐标迁移表。
- 需要图像组补图或重绘的资源缺口清单。
- 1080p 截图验收包。

### 验收标准

- 游戏窗口目标分辨率为 `1920x1080`。
- 背景、CG、立绘、消息框、选项、系统按钮不出现明显裁切、错位、黑边或压扁。
- 存读档、配置、标题、CG 页面都按 16:9 布局显示。
- 旧 4:3 资源若未完成重制，必须有明确占位策略和图像组待办编号。

### 时间计划

- 第 1 周：梳理旧坐标和窗口尺寸。
- 第 2 周：完成核心 ADV 画面 1080p 适配。
- 第 3 周：完成系统页面适配并交给 UI 组套视觉。
- 第 4 周：处理边角页面、截图验收、冻结坐标规范。

---

## UI 重做模块（ui-redesign）

### 负责人员

- 主负责人：凝言、盐甘草糖块
- 协助：虹映千夏

### 负责文件

- `system\Title.tjs`
- `system\ConfigWindow.tjs`
- `system\SystemWindow.tjs`
- `system\Album.tjs`
- `system\MessageFrame.tjs`
- `system\MessageArea.tjs`
- `system\SelectItem.tjs`
- `system\Confirm.tjs`
- `system\CgModeList.tjs`
- `system\CgSetupInfo.tjs`
- `frame\*.tlg`
- `rule\*.png`
- UI 输出新增资源目录建议：`work\ui-redesign\`，最终回灌前不得覆盖 `frame\`

### 禁止修改范围

- 不直接改剧本文字和跳转。
- 不直接改 BGM、SE、语音。
- 不直接替换 `tvpwin32.exe` 和 dll。
- 不把 AI 生成图直接覆盖原始 `frame\*.tlg`，必须先进入工作目录和 QA。

### 输入依赖

- resolution 模块的 1080p 坐标规范。
- image-remaster 的 UI 图高清化输出。
- image-qa 的视觉缺陷反馈。
- scenario-parser 提供的选项数量、文本长度、名称栏需求。

### 输出交付物

- UI 页面清单：标题、配置、存档、读档、消息框、选项、CG、回想、确认框。
- 每个页面的资源路径和脚本路径映射。
- 现代化 UI 视觉规范：字体、按钮状态、背景透明度、边距、色彩。
- 可测试的 UI 页面截图。

### 验收标准

- 所有主要页面在 1920x1080 下可读、可点击、状态清晰。
- 鼠标悬停、按下、禁用、选中状态都有明确视觉反馈。
- UI 图像没有明显 AI 伪影、错别字、边缘糊线或拉伸。
- 脚本中的按钮命中区域和视觉区域一致。

### 时间计划

- 第 2 周：完成页面结构和视觉规范。
- 第 3 周：完成标题、消息框、选项、配置页。
- 第 4 周：完成存读档、CG、回想、确认框。
- 第 5 周：全页面 QA 和视觉修正。

---

## UI 动画模块（ui-animation）

### 负责人员

- 主负责人：yuuki、凝言
- 协助：盐甘草糖块

### 负责文件

- `system\AnimationSequence.tjs`
- `system\Action.tjs`
- `system\AffineLayer.tjs`
- `system\Sprite.tjs`
- `system\EnvEffect.tjs`
- `system\EyeCatch.tjs`
- `system\GameSceneManager.tjs`
- `system\Title.tjs`
- `system\ConfigWindow.tjs`
- `system\SelectItem.tjs`
- `rule\*.png`

### 禁止修改范围

- 不改图像内容本身，只调用已验收资源。
- 不改变剧本跳转和剧情等待逻辑。
- 不加入会阻塞低配机器的高成本动画。
- 不在未经过 performance 模块确认前增加大量常驻层。

### 输入依赖

- UI-redesign 的页面状态和按钮状态。
- performance 的帧率、层数量、资源加载限制。
- resolution 的坐标和缩放规则。
- image-qa 对动画中图像边缘、补帧、转场瑕疵的反馈。

### 输出交付物

- UI 动画清单：标题入场、菜单切换、按钮反馈、选项出现、确认框、CG 页面切换。
- 每个动画的时长、缓动、层数量、触发条件。
- 可关闭或降级的低性能模式策略。
- 动画回归截图或录屏记录。

### 验收标准

- 动画不影响剧情推进和点击响应。
- 页面切换不出现残影、闪白、错误层级。
- 常规 UI 操作无明显卡顿。
- 所有动画都有明确入口和退出清理逻辑。

### 时间计划

- 第 3 周：制定动画规范并接入标题和按钮反馈。
- 第 4 周：接入系统页面和选项动画。
- 第 5 周：性能联调和降级策略。
- 第 6 周：冻结动画行为，只修复阻塞问题。

---

## 图片重制模块（image-remaster）

### 负责人员

- 主负责人：虹映千夏、03r
- 协助：凝言

### 负责文件

- `bg\*.tlg`
- `bg\*.png`
- `thumb\*.tlg`
- `frame\*.tlg`
- `rule\*.png`
- `content-data\*.tlg`
- 图像输出工作目录建议：
  - `work\image-remaster\source-list\`
  - `work\image-remaster\novelai\`
  - `work\image-remaster\waifu2x\`
  - `work\image-remaster\psd\`
  - `work\image-remaster\export-1920x1080\`

### 禁止修改范围

- 不直接改 `.ks`、`.tjs`、`.dll`、`.exe`。
- 不删除原始 `.tlg`。
- 不把未质检 AI 输出覆盖到游戏正式目录。
- 不改音频、字体、存档。

### 输入依赖

- resolution 模块提供的目标尺寸和裁切规则。
- UI-redesign 提供的 UI 素材需求。
- scenario-parser 提供的 CG、背景、立绘引用清单。
- image-qa 提供的返修意见。

### 输出交付物

- 图像批次清单：原文件、处理工具、参数、输出文件、负责人。
- 1920x1080 背景和 CG 输出。
- UI 图高清化输出。
- 可回灌资源包和差异预览图。

### 验收标准

- 输出图符合 16:9，目标为 `1920x1080` 或模块指定尺寸。
- 人物、建筑、文字、UI 边缘没有明显畸变。
- 所有 AI 扩图保留原图语义，不新增剧情冲突元素。
- 每张进入正式回灌候选的图都有来源和处理记录。

### 时间计划

- 第 1 周：建立资源清单和优先级。
- 第 2-4 周：按背景、CG、UI 图分批扩图和高清化。
- 第 5 周：集中返修 image-qa 标记问题。
- 第 6 周：交付最终回灌候选包。

---

## 图片 QA 模块（image-qa）

### 负责人员

- 主负责人：himahima、happy
- 协助：虹映千夏、03r

### 负责文件

- `work\image-remaster\export-1920x1080\*`
- `work\image-remaster\qa\*.md`
- `work\image-remaster\qa\*.csv`
- `bg\*.tlg`
- `thumb\*.tlg`
- `frame\*.tlg`
- `rule\*.png`

### 禁止修改范围

- 不直接修图覆盖源文件。
- 不直接改脚本和配置。
- 不删除图像组输出。
- 不把未确认问题标成已修复。

### 输入依赖

- image-remaster 的批次输出。
- UI-redesign 的页面截图。
- resolution 的 1080p 截图标准。
- scenario-parser 提供的图像出现位置和剧情上下文。

### 输出交付物

- 图像 QA 报告。
- 问题标注表：文件名、问题类型、严重等级、截图、建议处理人。
- 通过清单和返修清单。
- 最终图像验收签字记录。

### 验收标准

- 关键剧情背景、CG、立绘、UI 图全部经过人工查看。
- 每个问题都有文件路径和可复现截图。
- 严重问题不得进入 packaging 候选包。
- 已通过资源必须能对应到 image-remaster 批次记录。

### 时间计划

- 第 2 周：开始第一批背景和 UI 图 QA。
- 第 3-5 周：跟随 image-remaster 每批输出滚动 QA。
- 第 6 周：完成最终候选包验收。

---

## 性能优化模块（performance）

### 负责人员

- 主负责人：盐甘草糖块、yuuki
- 协助：凝言

### 负责文件

- `system\GameSceneManager.tjs`
- `system\AnimationSequence.tjs`
- `system\Action.tjs`
- `system\Sound.tjs`
- `system\Movie.tjs`
- `system\Sprite.tjs`
- `system\ADVScreen.tjs`
- `system\Album.tjs`
- `system\ConfigWindow.tjs`
- `system\Utility.tjs`
- 运行配置：`tvpwin32.cf`

### 禁止修改范围

- 不为了性能删除剧情、语音、CG 或 UI 功能。
- 不直接压缩或降质正式图像资源，必须先和 image-remaster 确认。
- 不替换用户运行入口，除非 packaging 和 engine-compat 同意。
- 不清理 `savedata\` 作为性能优化手段。

### 输入依赖

- ui-animation 的动画层数量和时长。
- resolution 的 1080p 资源尺寸。
- image-remaster 的最终图像体积。
- packaging 的安装后运行环境。

### 输出交付物

- 启动耗时、页面切换耗时、动画帧率、内存占用记录。
- 性能瓶颈清单。
- 已优化项和放弃优化项说明。
- 低配运行建议。

### 验收标准

- 启动和主要页面切换不会出现长时间无响应。
- 1080p 图像加载不造成持续卡顿。
- UI 动画在目标机器上基本流畅，必要时可降级。
- 性能优化不破坏剧情、声音、图像和 UI 状态。

### 时间计划

- 第 3 周：建立基线和监测方法。
- 第 4 周：优化启动、资源加载和页面切换。
- 第 5 周：联调 UI 动画和大图资源。
- 第 6 周：打包候选版性能验收。

---

## 打包发布模块（packaging）

### 负责人员

- 主负责人：happy
- 协助：凝言

### 负责文件

- `tvpwin32.exe`
- `*.dll`
- `*.cf`
- `icon\*.ico`
- `icon_change_instructions.txt`
- 最终交付包目录建议：
  - `dist\installer\`
  - `dist\portable\`
  - `dist\checksums\`
- 安装脚本目录建议：
  - `tools\packaging\`

### 禁止修改范围

- 不改剧情、图像、UI、音频内容。
- 不把 `savedata\`、调试日志、临时工作目录打进用户安装包。
- 不发布未经 image-qa 和功能 QA 通过的资源包。
- 不覆盖开发根目录中的运行依赖，必须先在 `dist\` 组包验证。

### 输入依赖

- engine-compat 的最终运行入口和依赖清单。
- scenario-parser 的剧情回归通过记录。
- resolution、ui-redesign、ui-animation 的页面验收截图。
- image-remaster 和 image-qa 的最终资源包。
- performance 的候选版性能结论。

### 输出交付物

- 用户可安装 exe。
- 便携版压缩包。
- 文件清单和 SHA256 校验。
- 安装、启动、卸载、重复安装验证记录。
- 发布说明。

### 验收标准

- 全新目录安装后可启动游戏。
- 安装包包含运行所需 exe、dll、cf、system、scenario、content-data、资源目录。
- 安装包不包含 `savedata\`、临时工作目录、未验收输出、调试垃圾。
- 安装后至少完成启动、进入标题、开始游戏、打开配置、存读档页面、退出的烟测。

### 时间计划

- 第 5 周：建立便携版组包清单。
- 第 6 周：制作安装包候选版。
- 第 7 周：安装、卸载、升级、杀毒误报和路径兼容验证。
- 第 8 周：发布最终包和校验文件。
