# 2026-05-29 waifu2x 放大、批处理、资源追踪工作记录

## 工作范围

项目路径：

`C:\Users\misakamiro\Downloads\Compressed\yosuga_krkrZ\yosuga_krkrZ`

本次只创建文档和只读资源状态表，未执行任何图像处理。

负责范围：

- `bg/*`
- `content-data/bg/*`
- `content-data/char/*`
- `content-data/thumb/*`
- `frame/*`
- `rule/*`

未修改范围：

- 未修改代码文件
- 未修改剧本文件
- 未修改原始图像资源
- 未运行 NovelAI
- 未运行 waifu2x
- 未生成 final 图

## 本次新增文档

- `docs/modules/waifu2x-pipeline/README.md`
- `docs/modules/waifu2x-pipeline/batch-plan.md`
- `docs/modules/waifu2x-pipeline/asset-tracking-table.md`
- `docs/modules/waifu2x-pipeline/parameter-standard.md`
- `docs/worklogs/2026-05-29-waifu2x-pipeline.md`

## 盘点结果

| 范围 | 扩展名 | 数量 | 当前策略 |
| --- | --- | ---: | --- |
| `bg/*` | `.tlg` | 116 | 背景图，计划 NovelAI + waifu2x |
| `bg/*` | `.png` | 1 | 0 字节占位图，跳过 |
| `content-data/bg/*` | `.tlg` | 116 | 背景镜像目录，计划去重后 NovelAI + waifu2x |
| `content-data/char/*` | `.tlg` | 2288 | 混合目录，先分类，再处理 |
| `content-data/thumb/*` | `.tlg` | 951 | 缩略图，等待 final 图重生成 |
| `frame/*` | `.tlg` | 197 | UI / 特效素材，条件 waifu2x |
| `rule/*` | `.png` | 57 | 转场规则图，默认保持原尺寸 |

合计：3726 个目标资源文件。

## 已建立规则

1. 所有输出进入 `remaster-output/waifu2x-pipeline`。
2. 不覆盖 `bg`、`content-data`、`frame`、`rule` 下的源文件。
3. 最终文件名必须包含源目录 slug、源文件 stem、阶段或目标、参数摘要、版本号。
4. `content-data/char` 不能整目录直接 NovelAI，必须先分类。
5. `content-data/thumb` 不直接放大，等 final 图稳定后重生成。
6. `rule` 默认不放大，除非确认引擎按全屏规则图采样。
7. QA 通过前不得进入 `05-final`。

## 失败样例记录要求

失败样例统一进入：

`remaster-output/waifu2x-pipeline/99-failed`

每个失败样例至少记录：

- 源路径
- 源 SHA256
- 输入阶段文件
- 输出阶段文件
- 工具名称和版本
- 参数
- 失败类型
- 截图或对比图路径
- 下一步建议

## 下一步处理建议

1. 先补充 manifest 脚本或手工清单，加入 SHA256、尺寸、alpha、分类字段。
2. 选 10 张样张跑完整链路，确认 NovelAI、手工修补、waifu2x、final、QA 的交接格式。
3. 样张通过后，优先处理 `bg` 与 `content-data/bg`。
4. 对 `content-data/char` 做前缀和尺寸分类，再拆成 CG / event、立绘、UI 副本三类。
5. `frame` 和 `rule` 等 UI / mask 类资源必须先做 alpha / 灰阶语义验证，不能直接套背景参数。
