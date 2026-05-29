# waifu2x 批处理计划

## 批处理边界

输入范围固定为：

- `bg/*`
- `content-data/bg/*`
- `content-data/char/*`
- `content-data/thumb/*`
- `frame/*`
- `rule/*`

禁止把输出写回上述目录。所有产物写入：

`remaster-output/waifu2x-pipeline`

## 阶段目录

| 阶段 | 目录 | 用途 |
| --- | --- | --- |
| 00 | `00-source-manifest` | 源文件清单、SHA256、尺寸、处理状态 |
| 01 | `01-decoded` | TLG 解码后的 PNG / TIFF 工作图 |
| 02 | `02-nai-outpaint` | NovelAI 扩图结果，仅背景和确认后的 CG 使用 |
| 03 | `03-manual-fix` | Photoshop / 手工修补工作文件和导出图 |
| 04 | `04-waifu2x` | waifu2x 批处理输出 |
| 05 | `05-final` | QA 通过后的最终图 |
| 06 | `06-qa` | QA 记录、失败截图、返工原因 |
| 99 | `99-failed` | 失败样例和不可处理文件记录 |

## 输入输出路径规则

源文件路径：

`<project-root>/<source-relative-path>`

阶段输出路径：

`remaster-output/waifu2x-pipeline/<stage>/<source-relative-directory>/<file-name-with-trace>.png`

示例：

| 源路径 | 阶段 | 输出路径 |
| --- | --- | --- |
| `bg/B01a.tlg` | 解码 | `remaster-output/waifu2x-pipeline/01-decoded/bg/B01a__src-bg__decoded__v001.png` |
| `bg/B01a.tlg` | NovelAI | `remaster-output/waifu2x-pipeline/02-nai-outpaint/bg/B01a__src-bg__nai16x9__seed000000__v001.png` |
| `bg/B01a.tlg` | waifu2x | `remaster-output/waifu2x-pipeline/04-waifu2x/bg/B01a__src-bg__waifu2x_noise1_scale2__v001.png` |
| `bg/B01a.tlg` | final | `remaster-output/waifu2x-pipeline/05-final/bg/B01a__src-bg__1920x1080__noise1_scale2__v001.png` |
| `frame/FRM_0101.tlg` | waifu2x | `remaster-output/waifu2x-pipeline/04-waifu2x/frame/FRM_0101__src-frame__waifu2x_noise0_scale2_alpha__v001.png` |

## 命名规则

最终图文件名必须保留这些追踪字段：

`<source-stem>__src-<source-dir-slug>__<target-or-stage>__<parameter-summary>__vNNN.<ext>`

目录 slug 规则：

| 源目录 | slug |
| --- | --- |
| `bg` | `src-bg` |
| `content-data/bg` | `src-content-data-bg` |
| `content-data/char` | `src-content-data-char` |
| `content-data/thumb` | `src-content-data-thumb` |
| `frame` | `src-frame` |
| `rule` | `src-rule` |

版本规则：

- 首次输出使用 `__v001`。
- 同参数重跑不得覆盖，使用 `__v002`。
- 参数变化时也必须递增版本，并在 QA 记录中说明变化。
- 最终图只从 QA 通过的阶段产物复制或导出，不直接从源图生成覆盖。

## 批处理顺序

1. 生成 manifest：登记源路径、大小、扩展名、SHA256、尺寸、alpha 状态、处理策略。
2. 解码 TLG：输出到 `01-decoded`，保留 alpha。
3. 分类：按目录、文件名前缀、尺寸、透明通道、调用位置判断类型。
4. NovelAI：只处理背景和确认后的 CG / event，不处理 UI、缩略图、转场规则图。
5. 手工修补：修 NovelAI 接缝、透视、重复纹理、脸手变形、文字伪影。
6. waifu2x：按 `parameter-standard.md` 执行分组批处理。
7. 尺寸归一：背景 / CG 最终归一到 `1920x1080`；UI / rule 保持批准尺寸。
8. QA：登记通过、返工、跳过或阻塞原因。
9. final：只接收 QA 通过的文件。

## 类型处理规则

| 类型 | NovelAI 扩图 | waifu2x | 最终输出 |
| --- | --- | --- | --- |
| 背景 `bg/*` | 需要，目标 16:9 | 需要 | `1920x1080` PNG |
| 背景镜像 `content-data/bg/*` | 需要，但先与 `bg/*` 去重 | 需要 | `1920x1080` PNG |
| CG / event 候选 `content-data/char/*` | 分类确认后需要 | 需要 | `1920x1080` PNG |
| 立绘 / 人物层 `content-data/char/*` | 默认不需要 | 需要，但保持 alpha 和裁切框 | 批准尺寸 PNG |
| 缩略图 `content-data/thumb/*` | 不需要 | 默认不直接放大 | 从 final 图重生成 |
| UI / frame `frame/*` | 不需要 | 条件需要 | 批准尺寸 PNG，保留 alpha |
| 转场 rule `rule/*` | 默认不需要 | 条件需要 | 默认保持 `800x600`，除非引擎确认需要全屏 |
| 0 字节占位图 | 不需要 | 不需要 | 跳过，只登记 |

## waifu2x 批处理参数摘要

| 分组 | model | noise | scale | tta | format | 说明 |
| --- | --- | ---: | ---: | --- | --- | --- |
| 背景 / CG | `anime_style_art_rgb` | 1 | 2 | 关 | PNG | 通用默认批处理 |
| 背景低噪样张 | `anime_style_art_rgb` | 0 | 2 | 关 | PNG | 已经被手工修干净的图 |
| UI / frame | `anime_style_art_rgb` | 0-1 | 2 | 关 | PNG | 必须复核 alpha 边缘 |
| 立绘 / 人物层 | `anime_style_art_rgb` | 1 | 2 | 关，终版样张可开 | PNG | 不改变裁切框和站位 |
| rule | `anime_style_art_rgb` | 0 | 1 或 2 | 关 | PNG | 默认不改变尺寸；如 scale 2，最后回采样 |
| 缩略图 | 不建议直接使用 | - | - | - | PNG | 从最终图重新生成 |

## 失败样例

| 失败类型 | 例子 | 处理方式 |
| --- | --- | --- |
| 解码失败 | TLG 解码输出全黑、透明丢失、尺寸异常 | 移入 `99-failed/decode`，记录工具版本和源 SHA256 |
| NovelAI 偏离 | 生成新人物、新文字、水印、错误物件 | 标记 `返工-NovelAI`，重新调 prompt / seed / strength |
| 扩图接缝 | 左右扩图边缘重复纹理、透视断裂 | 进入 `03-manual-fix` 手工修补后再 waifu2x |
| waifu2x 过锐 | 线条锯齿、颗粒被强化、皮肤塑料感 | 降噪从 `1` 降到 `0` 或改用手工修补图重跑 |
| alpha 破坏 | UI 边缘黑边、白边、半透明阴影变硬 | 标记 `返工-alpha`，改用保 alpha 的解码和输出链路 |
| 尺寸错误 | 背景 final 不是 `1920x1080` | 不得进 `05-final`，回到尺寸归一阶段 |
| 输出覆盖风险 | 目标文件已存在但脚本尝试覆盖 | 停止批处理，递增版本号后重跑 |
| 缩略图裁切错误 | 人脸、标题、关键道具被裁掉 | 从 final 图重新裁切，不能直接拉伸旧缩略图 |

## 下一步处理建议

1. 先做 10 张样张：背景 3、`content-data/char` CG 候选 3、立绘 1、frame 1、rule 1、thumb 1。
2. 对样张记录完整参数、输出路径和 QA 结论。
3. 样张通过后再按目录批处理，优先 `bg` 和 `content-data/bg`。
4. `content-data/char` 必须先分类，不能整目录直接 NovelAI。
5. `content-data/thumb` 等 final 图稳定后统一重生成，不先投入 waifu2x。
