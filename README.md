# 缘之空 krkrZ 重制 / 1920x1080 适配项目

本仓库用于管理《缘之空》从 krkr2 到 krkrZ 的移植、`1920x1080` 16:9 适配、UI 重做、图像高清化、性能优化和最终打包发布工作。

## 当前目标

1. 完成 krkr2 到 krkrZ 的引擎迁移，并修复启动链、兼容 API、选项跳转等问题。
2. 将游戏逻辑分辨率从 `800x600` 提升到 `1920x1080`，适配 16:9 无黑边显示。
3. 使用 NovelAI、waifu2x、Photoshop 等工具重制背景、CG、立绘、UI、缩略图等资源。
4. 重新设计 UI，使标题、对话框、设置、存档/读档、历史、CG 模式等页面适配 1080p 并更现代化。
5. 改进 UI 动画，保证动画流畅、可降级、不会影响剧情推进和点击响应。
6. 优化 krkrZ 下的运行性能，降低大图、转场、环境效果、音频和动画带来的占用。
7. 打包成用户可直接安装或解压运行的版本，并提供校验文件和安装验证记录。

## 文档入口

总控文档：

- `docs/project/00-project-overview.md`：项目总览。
- `docs/project/01-file-module-split.md`：模块和文件边界。
- `docs/project/02-personnel-task-plan.md`：人员分工和负责路径。
- `docs/project/03-schedule.md`：时间计划。
- `docs/project/04-handoff-template.md`：交接单模板。
- `docs/project/05-progress-report-template.md`：进度报告模板。

按人员拆分后的资料包：

- `docs/personnel/README.md`：人员责任包索引。
- `docs/personnel/<人员名>/README.md`：每个人的职责、阅读顺序、负责文件、禁止事项和交付要求。
- `docs/personnel/<人员名>/modules/`：该人员负责或协作模块的文档副本。
- `docs/personnel/<人员名>/worklogs/`：该人员相关模块的工作日志副本。
- `docs/personnel/<人员名>/templates/`：交接模板和进度报告模板。

## 每个人应该先看哪些文件

### 凝言

主负责：`engine-compat`、`ui-redesign`

先看：

- `docs/personnel/凝言/README.md`
- `docs/personnel/凝言/modules/engine-compat/README.md`
- `docs/personnel/凝言/modules/engine-compat/current-bugs.md`
- `docs/personnel/凝言/modules/ui-redesign/README.md`
- `docs/personnel/凝言/modules/ui-redesign/screen-map.md`
- `docs/personnel/凝言/modules/ui-animation/animation-upgrade-plan.md`
- `docs/personnel/凝言/templates/04-handoff-template.md`

### 盐甘草糖块

主负责：`resolution-1920x1080`、`performance`

先看：

- `docs/personnel/盐甘草糖块/README.md`
- `docs/personnel/盐甘草糖块/modules/resolution-1920x1080/README.md`
- `docs/personnel/盐甘草糖块/modules/resolution-1920x1080/current-800x600-map.md`
- `docs/personnel/盐甘草糖块/modules/resolution-1920x1080/migration-plan.md`
- `docs/personnel/盐甘草糖块/modules/performance/README.md`
- `docs/personnel/盐甘草糖块/modules/performance/current-risk-list.md`
- `docs/personnel/盐甘草糖块/modules/ui-redesign/layout-standard.md`

### yuuki

主负责：`scenario-parser`、`ui-animation`

先看：

- `docs/personnel/yuuki/README.md`
- `docs/personnel/yuuki/modules/scenario-parser/README.md`
- `docs/personnel/yuuki/modules/scenario-parser/tag-support-map.md`
- `docs/personnel/yuuki/modules/scenario-parser/select-jump-analysis.md`
- `docs/personnel/yuuki/modules/ui-animation/README.md`
- `docs/personnel/yuuki/modules/ui-animation/current-animation-map.md`
- `docs/personnel/yuuki/modules/engine-compat/current-bugs.md`

### happy

主负责：`packaging`、`image-qa`、功能验收

先看：

- `docs/personnel/happy/README.md`
- `docs/personnel/happy/modules/packaging/README.md`
- `docs/personnel/happy/modules/packaging/release-file-list.md`
- `docs/personnel/happy/modules/packaging/exclude-list.md`
- `docs/personnel/happy/modules/packaging/final-test-plan.md`
- `docs/personnel/happy/modules/image-qa/README.md`
- `docs/personnel/happy/modules/image-qa/qa-checklist.md`

### 虹映千夏

主负责：`image-remaster`

先看：

- `docs/personnel/虹映千夏/README.md`
- `docs/personnel/虹映千夏/modules/image-remaster/README.md`
- `docs/personnel/虹映千夏/modules/image-remaster/processing-standard.md`
- `docs/personnel/虹映千夏/modules/image-remaster/output-naming-standard.md`
- `docs/personnel/虹映千夏/modules/image-remaster/sample-list.md`
- `docs/personnel/虹映千夏/modules/ui-redesign/asset-request-list.md`
- `docs/personnel/虹映千夏/modules/image-qa/pass-standard.md`

### himahima

主负责：`image-qa`

先看：

- `docs/personnel/himahima/README.md`
- `docs/personnel/himahima/modules/image-qa/README.md`
- `docs/personnel/himahima/modules/image-qa/qa-checklist.md`
- `docs/personnel/himahima/modules/image-qa/pass-standard.md`
- `docs/personnel/himahima/modules/image-qa/rework-list.md`
- `docs/personnel/himahima/modules/image-remaster/sample-list.md`

### 03r

主负责：`image-remaster`、`waifu2x-pipeline`

先看：

- `docs/personnel/03r/README.md`
- `docs/personnel/03r/modules/image-remaster/README.md`
- `docs/personnel/03r/modules/image-remaster/processing-standard.md`
- `docs/personnel/03r/modules/image-remaster/output-naming-standard.md`
- `docs/personnel/03r/modules/waifu2x-pipeline/README.md`
- `docs/personnel/03r/modules/waifu2x-pipeline/batch-plan.md`
- `docs/personnel/03r/modules/image-qa/qa-checklist.md`

## 模块文档

- `docs/modules/engine-compat/`：引擎兼容、启动链、当前 bug、修复计划。
- `docs/modules/scenario-parser/`：剧本标签、选项跳转、回归测试。
- `docs/modules/resolution-1920x1080/`：分辨率迁移、旧坐标映射、UI 尺寸需求。
- `docs/modules/ui-redesign/`：UI 页面映射、布局标准、素材需求。
- `docs/modules/ui-animation/`：当前动画映射、升级计划、性能风险。
- `docs/modules/image-remaster/`：图片重制流程、命名标准、样张清单。
- `docs/modules/waifu2x-pipeline/`：waifu2x 批处理计划、参数标准、资源跟踪表。
- `docs/modules/image-qa/`：图片 QA 标准、检查清单、返工列表。
- `docs/modules/performance/`：性能风险、优化计划、测试计划。
- `docs/modules/packaging/`：发布文件清单、排除清单、安装器计划、最终测试计划。

## 工作规则

1. 先看自己目录下的 `README.md`，再看对应 `modules/` 文档。
2. 修改跨模块文件前，先填写交接单：`docs/project/04-handoff-template.md`。
3. 阶段完成后填写进度报告：`docs/project/05-progress-report-template.md`。
4. 图像资源不得绕过 QA 直接覆盖正式目录。
5. 打包前必须排除 `savedata/`、日志、临时输出和未验收文件。
6. 大文件通过 Git LFS 管理，避免超过 GitHub 单文件限制。

## 当前仓库说明

本仓库包含当前项目文件、脚本、资源、文档和人员责任包。`savedata/` 属于本机用户数据，不进入仓库。后续如需要发布用户安装包，应以 `docs/modules/packaging/` 中的规则重新生成 `dist/` 交付物。
