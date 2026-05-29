# krkrZ 性能模块交接

## 目标

本模块记录当前 krkrZ 迁移后的性能风险分析。本轮只做文档，不优化运行时脚本、不修改图片资源、不修改剧本文件，也不动封包文件。

## 已分配范围

已审查文件：

- `tvpwin32.cf`
- `startup.tjs`
- `k2compat.tjs`
- `system/Window.tjs`
- `system/ADVScreen.tjs`
- `system/Sound.tjs`
- `system/Sprite.tjs`
- `system/AnimationSequence.tjs`
- `system/EnvEffect.tjs`

只读参考：

- `system/Status.tjs`：当前 `WINDOW_WIDTH = 800`、`WINDOW_HEIGHT = 600`。
- `docs/modules/resolution-1920x1080/*`：现有 `1920x1080` 迁移方向。
- 资源清单命令：只用于评估大 TLG/PNG 风险，没有修改资源。

## 当前结论

目前最高的 CPU/GPU 风险不是单一泄漏，而是连续 handler、短间隔 timer、全屏层复制、转场缓冲和环境效果叠加后持续重绘。

当前基础画布仍是 `800x600`，所以多数全屏操作现在还可控。项目迁移到 `1920x1080` 后，同样的全屏缓冲和复制像素量约为原来的 4.32 倍。因此在改性能前，最应该先测量的是：`30ms` 环境效果定时器、全画布转场复制、雨效果填充、雪粒子、背景滚动循环。

## 文档索引

- `current-risk-list.md`：当前 timer、层、图片加载、音频缓冲、动画和 1080p 迁移风险。
- `optimization-plan.md`：低风险优化顺序，以及当前不建议做的事。
- `test-plan.md`：静态和运行时验证方法。
- `../../worklogs/2026-05-29-performance.md`：本轮分析日志。

## 推荐下一步

不要一开始就改分辨率、粒子数量或素材格式。先采集基线：标题待机、无特效 ADV、有雨/雪/背景滚动 ADV、BGM 加语音、跳过/自动、存档/读档截图路径。之后一次只优化一类运行时工作，并比较 CPU、内存、卡顿和视觉回归。
