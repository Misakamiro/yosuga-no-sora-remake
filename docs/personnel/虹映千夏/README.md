# 虹映千夏责任包

## 职责定位

虹映千夏负责图像方向，主负责 `image-remaster`，协助 `ui-redesign` 和 `image-qa`。

核心目标：

- 使用 NovelAI 4.5 和 Photoshop 完成背景、CG、UI 图的 16:9 扩图和人工修复。
- 保留每张图的来源、参数、工作文件和输出路径。
- 在正式回灌前交给 QA 人员确认。

## 优先阅读顺序

1. `00-project-overview.md`
2. `modules/image-remaster/README.md`
3. `modules/image-remaster/processing-standard.md`
4. `modules/image-remaster/output-naming-standard.md`
5. `modules/image-remaster/sample-list.md`
6. `modules/ui-redesign/asset-request-list.md`
7. `modules/image-qa/pass-standard.md`

## 模块目录

- `modules/image-remaster`
- `modules/ui-redesign`
- `modules/image-qa`

## 主要负责路径

- `work/image-remaster/novelai/*`
- `work/image-remaster/psd/*`
- `work/image-remaster/export-1920x1080/*`
- `bg/*.tlg` 的候选替换图。
- `frame/*.tlg` 的 UI 候选替换图。
- `thumb/*.tlg` 的候选替换图。

## 禁止事项

- 不直接覆盖游戏正式资源。
- 不改脚本文件。
- 不把未标注来源的 AI 图交付为最终图。
- 不跳过 himahima/happy 的 QA。

## 交付要求

- 每批图像必须有来源文件、工具、参数、输出路径。
- Photoshop 修改保留可编辑源文件。
- UI 图必须能对应凝言的 UI 页面需求。
- 进入回灌候选前必须进入 QA 清单。
