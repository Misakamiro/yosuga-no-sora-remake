# 03r 责任包

## 职责定位

03r 负责图像处理，主负责 `image-remaster` 和 `waifu2x-pipeline`，协助 `image-qa`。

核心目标：

- 按批次执行 waifu2x 放大、去噪和候选输出整理。
- 保留处理参数，避免不同工具输出混放。
- 配合虹映千夏分工，避免同一资源重复处理。

## 优先阅读顺序

1. `00-project-overview.md`
2. `modules/image-remaster/README.md`
3. `modules/image-remaster/processing-standard.md`
4. `modules/image-remaster/output-naming-standard.md`
5. `modules/waifu2x-pipeline/README.md`
6. `modules/waifu2x-pipeline/batch-plan.md`
7. `modules/image-qa/qa-checklist.md`

## 模块目录

- `modules/image-remaster`
- `modules/waifu2x-pipeline`
- `modules/image-qa`

## 主要负责路径

- `work/image-remaster/waifu2x/*`
- `work/image-remaster/export-1920x1080/*`
- `bg/*.tlg` 的候选输出。
- `thumb/*.tlg` 的候选输出。
- `frame/*.tlg` 的候选输出。

## 禁止事项

- 不直接覆盖正式资源。
- 不改 `.ks`、`.tjs`、`.cf`。
- 不把低质量放大图交给 packaging。
- 不混放 NovelAI、Photoshop、waifu2x 不同阶段产物。

## 交付要求

- 按批次处理并记录输入、输出、参数、负责人。
- 高清化结果必须保留处理参数。
- 与虹映千夏分工时记录资源归属，避免重复处理。
- 输出进入正式候选前必须经过 image-qa。
