# waifu2x 放大、批处理、资源追踪

## 模块目标

本模块负责为指定图片资源建立可追踪的 waifu2x 批处理流程。当前阶段只创建处理规范、批处理计划、参数标准和资源状态表，不运行 NovelAI，不运行 waifu2x，不生成最终图片，也不覆盖任何原始资源。

项目根目录：

`C:\Users\misakamiro\Downloads\Compressed\yosuga_krkrZ\yosuga_krkrZ`

独立输出根目录：

`remaster-output/waifu2x-pipeline`

## 负责范围

本模块只负责以下目录中的资源登记和处理规划：

- `bg/*`
- `content-data/bg/*`
- `content-data/char/*`
- `content-data/thumb/*`
- `frame/*`
- `rule/*`

不包含 `thumb/*`、`content-data/frame/*`、`content-data/rule/*`，除非后续单独授权扩展范围。

## 禁止修改

- 代码文件，例如 `.tjs`、`.dll`、`.exe`
- 剧本文件，例如 `scenario/*`
- 原始图像资源，例如 `bg/*`、`content-data/*`、`frame/*`、`rule/*`
- 打包文件、运行时文件、存档文件

所有处理产物必须写入 `remaster-output/waifu2x-pipeline`，并按原始相对路径保留目录结构，保证最终图能追踪回源文件。

## 文档索引

- `batch-plan.md`：批处理规则、输入输出路径、阶段状态和失败样例
- `asset-tracking-table.md`：每张图的源路径、是否需要 NovelAI、是否需要 waifu2x、输出路径和 QA 状态
- `parameter-standard.md`：waifu2x 参数建议、按资源类型的参数分组和 QA 标准
- `docs/worklogs/2026-05-29-waifu2x-pipeline.md`：本次工作记录

## 当前结论

本次盘点覆盖 3726 个目标资源文件：

| 范围 | 文件数 | 当前策略 |
| --- | ---: | --- |
| `bg/*` | 117 | 背景图进入 NovelAI 扩图 + waifu2x；0 字节占位图跳过 |
| `content-data/bg/*` | 116 | 背景镜像目录，进入 NovelAI 扩图 + waifu2x，需与 `bg/*` 做去重确认 |
| `content-data/char/*` | 2288 | 混合目录，先分类，再决定是否 NovelAI；有效图默认需要 waifu2x |
| `content-data/thumb/*` | 951 | 缩略图不直接放大，等待最终图重生成缩略图 |
| `frame/*` | 197 | UI / 特效素材，默认不 NovelAI；按 alpha 和边缘质量决定是否 waifu2x |
| `rule/*` | 57 | 转场规则 PNG，默认保留原尺寸；仅在引擎确认全屏规则图需要升级时处理 |

## 不覆盖原则

任何批处理命令都必须满足：

1. 输入路径只读。
2. 输出路径位于 `remaster-output/waifu2x-pipeline`。
3. 输出文件名包含源目录 slug、源文件 stem、处理阶段、参数摘要、版本号。
4. 若输出文件已存在，必须新建 `__v002`、`__v003`，不得覆盖。
5. QA 通过前不得进入最终交付目录。
