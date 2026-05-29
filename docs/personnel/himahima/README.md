# himahima 责任包

## 职责定位

himahima 负责图像 QA，主负责 `image-qa`，协助图片返修确认。

核心目标：

- 对背景、CG、立绘、UI、缩略图做人工检查。
- 将问题按文件、位置、严重等级和建议处理人记录。
- 明确区分通过清单和返修清单。

## 优先阅读顺序

1. `00-project-overview.md`
2. `modules/image-qa/README.md`
3. `modules/image-qa/qa-checklist.md`
4. `modules/image-qa/pass-standard.md`
5. `modules/image-qa/rework-list.md`
6. `modules/image-remaster/sample-list.md`

## 模块目录

- `modules/image-qa`
- `modules/image-remaster`

## 主要负责路径

- `work/image-remaster/qa/*`
- `work/image-remaster/export-1920x1080/*`
- 图像截图和人工标注文件。

## 禁止事项

- 不直接修图覆盖资源。
- 不改代码。
- 不把未检查资源标为通过。
- 不删除图像组输出。

## 交付要求

- 每张问题图必须写文件名、问题位置、问题描述、建议处理人。
- 剧情关键图、人物脸部、文字、UI 边缘优先检查。
- 通过和返修必须分开列表。
- 阻塞级问题不得进入 packaging 候选包。
