# 人员任务计划

项目根目录：

`C:\Users\misakamiro\Downloads\Compressed\yosuga_krkrZ\yosuga_krkrZ`

## 人员总表

| 人员 | 类型 | 工具/方式 | 主模块 | 协作模块 |
| --- | --- | --- | --- | --- |
| 凝言 | 代码 + 图像 | Claude Code / Codex | `engine-compat`、`ui-redesign` | `ui-animation`、`image-remaster`、`packaging` |
| 盐甘草糖块 | 代码 | Codex | `resolution-1920x1080`、`performance` | `scenario-parser`、`ui-redesign` |
| yuuki | 代码 | Claude Code | `scenario-parser`、`ui-animation` | `engine-compat`、`performance` |
| happy | 代码/人工组 | 人工组 | `packaging`、`image-qa` | 功能验收、安装验收 |
| 虹映千夏 | 图像 | NovelAI4.5 + Photoshop | `image-remaster` | `ui-redesign`、`image-qa` |
| himahima | 图像 | 不使用 agent | `image-qa` | 图像返修确认 |
| 03r | 图像 | 图像制作 | `image-remaster` | `image-qa` |

## 凝言

### 负责模块

- `engine-compat`
- `ui-redesign`
- 协助 `ui-animation`
- 协助 `image-remaster`
- 协助 `packaging`

### 负责路径

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
- `system\Title.tjs`
- `system\ConfigWindow.tjs`
- `system\MessageFrame.tjs`
- `system\MessageArea.tjs`
- `frame\*.tlg` 的 UI 回灌协调，不直接跳过 QA 覆盖。

### 交付要求

- 写明每次兼容层修改影响的 API、类、函数或加载顺序。
- UI 修改必须附 1920x1080 截图。
- 图像回灌必须引用 image-qa 的通过清单。
- packaging 协助时只确认依赖，不替 happy 直接发布。

### 禁止事项

- 不直接改 `scenario\*.ks` 剧情内容。
- 不删除 `.bak`、`.corrupted`。
- 不把未 QA 图像覆盖到正式资源目录。

## 盐甘草糖块

### 负责模块

- `resolution-1920x1080`
- `performance`
- 协助 `scenario-parser`
- 协助 `ui-redesign`

### 负责路径

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
- `system\GameSceneManager.tjs`
- `system\AnimationSequence.tjs`
- `system\Action.tjs`
- `system\Sound.tjs`
- `system\Movie.tjs`
- `system\Sprite.tjs`
- `system\Utility.tjs`
- `system\bgData.csv`
- `bgData.csv`
- `tvpwin32.cf`

### 交付要求

- 每个坐标修改要写旧值、新值、影响页面。
- 性能优化要写修改前后基线，不允许只写主观感觉。
- 涉及选项、消息框和名称栏时必须通知 yuuki。
- 涉及 UI 页面结构时必须通知凝言。

### 禁止事项

- 不直接重绘图像。
- 不修改剧情语义。
- 不用删除动画或资源的方式掩盖性能问题。

## yuuki

### 负责模块

- `scenario-parser`
- `ui-animation`
- 协助 `engine-compat`
- 协助 `performance`

### 负责路径

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
- `system\AnimationSequence.tjs`
- `system\Action.tjs`
- `system\AffineLayer.tjs`
- `system\Sprite.tjs`
- `system\EnvEffect.tjs`
- `system\EyeCatch.tjs`
- `system\GameSceneManager.tjs`

### 交付要求

- 建立 `.ks` 路线分组、标签、选项、跳转清单。
- 动画改动必须写触发条件、清理条件、层数量。
- 剧本解析问题必须附具体脚本文件和标签位置。
- 影响兼容层时先交给凝言确认。

### 禁止事项

- 不删除剧情段落或跳转来规避解析错误。
- 不替换图像资源。
- 不在动画中新增无法退出的常驻层。

## happy

### 负责模块

- `packaging`
- `image-qa`
- 功能验收

### 负责路径

- `dist\installer\*`
- `dist\portable\*`
- `dist\checksums\*`
- `tools\packaging\*`
- `icon\*.ico`
- `icon_change_instructions.txt`
- `work\image-remaster\qa\*`

### 交付要求

- 安装包必须写清包含文件、排除文件、SHA256。
- 每个候选包必须做安装、启动、卸载验证。
- 图像 QA 要标清严重等级：阻塞、高、中、低。
- 人工验收记录必须能对应到具体版本和包路径。

### 禁止事项

- 不把 `savedata\` 打进发布包。
- 不发布未通过 QA 的图像或脚本。
- 不直接修改代码来临时绕过打包问题。

## 虹映千夏

### 负责模块

- `image-remaster`
- 协助 `ui-redesign`

### 负责路径

- `work\image-remaster\novelai\*`
- `work\image-remaster\psd\*`
- `work\image-remaster\export-1920x1080\*`
- `bg\*.tlg` 的候选替换图。
- `frame\*.tlg` 的 UI 候选替换图。
- `thumb\*.tlg` 的候选替换图。

### 交付要求

- 每批图像必须有来源文件、工具、参数、输出路径。
- Photoshop 修改必须保留可编辑源文件。
- 进入回灌候选前必须交给 himahima/happy 做 QA。
- UI 图要与凝言的 UI 页面需求对应。

### 禁止事项

- 不直接覆盖游戏正式资源。
- 不改脚本文件。
- 不把未标注来源的 AI 图交付为最终图。

## himahima

### 负责模块

- `image-qa`

### 负责路径

- `work\image-remaster\qa\*`
- `work\image-remaster\export-1920x1080\*`
- 图像截图和人工标注文件。

### 交付要求

- 每张有问题的图必须写文件名、问题位置、问题描述、建议处理人。
- 对剧情关键图、人物脸部、文字、UI 边缘优先检查。
- 通过和返修必须分开列表。

### 禁止事项

- 不直接修图覆盖资源。
- 不改代码。
- 不把未检查资源标为通过。

## 03r

### 负责模块

- `image-remaster`
- 协助 `image-qa`

### 负责路径

- `work\image-remaster\waifu2x\*`
- `work\image-remaster\export-1920x1080\*`
- `bg\*.tlg`、`thumb\*.tlg`、`frame\*.tlg` 的候选输出。

### 交付要求

- 按批次处理，不混放不同工具输出。
- 高清化结果必须保留处理参数。
- 与虹映千夏分工时避免同一资源重复处理。

### 禁止事项

- 不直接覆盖正式资源。
- 不改 `.ks`、`.tjs`、`.cf`。
- 不把低质量放大图交给 packaging。

## 跨人员交接规则

1. 代码到图像：必须提供截图、目标尺寸、资源名、页面位置。
2. 图像到代码：必须提供原图路径、输出路径、尺寸、QA 状态。
3. 代码到打包：必须提供运行入口、依赖 dll、排除目录、烟测步骤。
4. QA 到执行人员：必须提供复现路径、截图、严重等级、期望结果。
5. 任何跨模块修改都必须填写 `04-handoff-template.md`。
