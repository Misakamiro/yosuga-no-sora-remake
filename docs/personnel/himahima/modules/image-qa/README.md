# 图像人工质检与返工清单

## 模块目标

本模块负责检查 NovelAI 扩图、waifu2x 放大、Photoshop 修图以及 UI 图清晰度的最终交付质量，并输出可执行的返工清单。

项目路径：

`C:\Users\misakamiro\Downloads\Compressed\yosuga_krkrZ\yosuga_krkrZ`

## 本轮检查结论

检查日期：2026-05-29

本轮只读扫描项目目录后，未发现标准化的最终图输出目录，例如：

- `remaster-output/image-remaster/05-final`
- `remaster-output/image-remaster/06-qa`
- `NovelAI`
- `waifu2x`
- `Photoshop`
- `processed`
- `upscale`

当前唯一发现的处理后图片候选为：

`bg_output/B01a.png`

该文件为 0 字节，无法打开、无法读取尺寸、无法进行画面质检，因此不能作为已扩图、已放大或已修图成品验收。

## 统计

| 项目 | 数量 | 说明 |
| --- | ---: | --- |
| 已实际检查的处理后图 | 1 | `bg_output/B01a.png` |
| 已通过 | 0 | 没有可验收成品 |
| 返工 | 1 | `bg_output/B01a.png` 为 0 字节 |
| 未处理样张 | 5 | 沿用 `image-remaster` 模块登记的 S01-S05 样张 |

当前返工交给谁处理：

| 返工项 | 交给谁处理 | 原因 |
| --- | --- | --- |
| `bg_output/B01a.png` | NovelAI 扩图负责人 | 空文件，需从 `bg/B01a.tlg` 重新导出并重跑扩图 |

## 职责边界

允许本模块修改：

- `docs/modules/image-qa/*`
- `docs/worklogs/*-image-qa.md`

禁止本模块修改：

- 代码文件，例如 `.tjs`、`.dll`、`.exe`
- 剧本文件，例如 `scenario/*`
- 原始资源文件，例如 `.tlg`、原始 `.png`、`.bmp`、`.ico`
- 已处理图片文件本体

## 状态定义

| 状态 | 含义 |
| --- | --- |
| 未处理 | 尚未发现对应 NovelAI / PS / waifu2x 阶段输出 |
| 已扩图 | 已有 NovelAI outpainting 或等价扩图结果，但尚未完成最终验收 |
| 已放大 | 已有 waifu2x 放大结果，但尚未完成最终验收 |
| 需 PS 修复 | 画面主体基本可用，但存在接缝、边缘、色调、穿帮等人工修图问题 |
| 需重跑 NovelAI | 扩图结果缺失、损坏、尺寸错误，或生成内容改变主体/剧情信息 |
| 需重跑 waifu2x | 放大结果缺失、损坏、过锐、糊边、噪点或尺寸不合格 |
| 已通过 | 尺寸、清晰度、人物、背景、UI 边缘和记录信息均符合验收标准 |

## 交接文件

- `qa-checklist.md`：逐图/逐类检查状态
- `rework-list.md`：返工清单
- `pass-standard.md`：通过标准
- `docs/worklogs/2026-05-29-image-qa.md`：本轮工作日志
