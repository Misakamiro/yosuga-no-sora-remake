# 2026-05-29 性能工作日志

## 任务

分析当前 krkrZ 引擎性能风险并只创建文档，本轮不优化代码。

## 已检查文件

- `tvpwin32.cf`
- `startup.tjs`
- `k2compat.tjs`
- `system/Window.tjs`
- `system/ADVScreen.tjs`
- `system/Sound.tjs`
- `system/Sprite.tjs`
- `system/AnimationSequence.tjs`
- `system/EnvEffect.tjs`

只读辅助上下文：

- `system/Status.tjs`
- `docs/modules/resolution-1920x1080/README.md`
- `docs/modules/resolution-1920x1080/migration-plan.md`
- 用于评估大文件风险的资源清单。

## 已运行命令

```powershell
Get-ChildItem -Force
git status --short
rg --files
rg -n "Timer|interval|onTimer|setInterval|tick|invalidate|update|sleep|fps|frame|enabled|stop|start|finalize|dispose|free|loadImages|loadImage|loadStorage|setImage|assignImages|Image|Layer|SoundBuffer|WaveSoundBuffer|play|loop|Animation|Env|snow|rain" tvpwin32.cf startup.tjs k2compat.tjs system/Window.tjs system/ADVScreen.tjs system/Sound.tjs system/Sprite.tjs system/AnimationSequence.tjs system/EnvEffect.tjs
rg -n "new Timer|interval =|\\.interval|enabled = true|enabled = false|addContinuousHandler|removeContinuousHandler" startup.tjs k2compat.tjs system/Window.tjs system/ADVScreen.tjs system/Sound.tjs system/Sprite.tjs system/AnimationSequence.tjs system/EnvEffect.tjs
rg -n "new global\\.Layer|new Layer|invalidate|free|assignImages|loadImages|setImageSize|setSizeToImageSize|piledCopy|copyRect|setImagePos" system/ADVScreen.tjs system/Sprite.tjs system/AnimationSequence.tjs system/EnvEffect.tjs system/Window.tjs
rg -n "SoundBuffer|WaveSoundBuffer|PlayBgm|StopBgm|PlaySe|StopSe|PlayEnvSe|StopEnvSe|PlayVoice|StopVoice|PlayBgv|StopBgv|BGM|SE|VOICE|BGV|Sound" system/Sound.tjs system/ADVScreen.tjs k2compat.tjs
rg -n "WINDOW_WIDTH|WINDOW_HEIGHT|WINDOW_ZOOM|BG_RANGE|EVENT_SIZE_CHECK|SCREEN" system tvpwin32.cf startup.tjs k2compat.tjs
Get-ChildItem -Recurse -Include *.tlg -File | Sort-Object Length -Descending | Select-Object -First 30 ...
```

`git status --short` 显示该目录不是 Git 仓库。

## 当前发现的风险

- `tvpwin32.cf` 中有 `contfreq = 60`、`drawthread = auto`、`debugwin = yes`、`usedb = yes`、`aamethod = res4`。
- `startup.tjs` 启用兼容层，并在启动阶段输出 debug message。
- `system/Window.tjs` 有 `1000ms` 鼠标自动隐藏 timer，以及只在跟踪期间启用的 `10ms` 鼠标跟踪 timer。
- `system/ADVScreen.tjs` 创建自动 timer，启动/停止连续 action 回调，创建环境效果对象，并使用全画布转场复制。
- `system/Sound.tjs` 每个 `SoundLayer` 使用 `1000ms` 清理 timer；BGM 用双缓冲实现交叉淡入；支持循环 ENVSE/BGV 和公开 stop/fade 函数。
- `system/Sprite.tjs` 使用连续 handler 执行透明度/移动/缩放/旋转 activation。
- `system/AnimationSequence.tjs` 使用 wait timer，并在序列内共享 image assignment，finalize/clear/list clean 有清理路径。
- `system/EnvEffect.tjs` 是最清楚的高频工作来源：基础 `20ms` 效果 timer，雪/雨 `30ms` 绘制间隔，雪生成 timer，雨填充/粒子循环，背景滚动 tiled affine copy 循环。
- 当前逻辑画布仍是 `800x600`，但项目文档已经规划 `1920x1080` 迁移。迁移后全画布操作会明显更贵。

## 已创建文档

- `docs/modules/performance/README.md`
- `docs/modules/performance/current-risk-list.md`
- `docs/modules/performance/optimization-plan.md`
- `docs/modules/performance/test-plan.md`
- `docs/worklogs/2026-05-29-performance.md`

## 建议

先测量，再按以下顺序优化：

1. 为标题、ADV、转场、效果、音频、存档/读档建立 CPU/内存/卡顿基线。
2. 兼容稳定后，只在性能/发布构建中关闭或限制 debug 输出。
3. 结合截图和 CPU 对比调优环境效果。
4. 改 handler 行为前，先加入动画/连续 handler 诊断。
5. 只有有重复加载证据时，才审查图片重载避免策略。
6. 只有 stop/fade 后 buffer 仍偏高时，才调音频清理。
7. `1920x1080` 分支存在后，再处理全画布操作。

## 当前不要改

- 本文档任务不改运行时脚本。
- 不改图片资源、剧本文件或封包文件。
- 不全局降低 `contfreq`。
- 不移除 BGM 交叉淡入双缓冲。
- 没有运行证据前不加全局图片缓存。
- 不把 `1920x1080` 迁移和性能优化混在同一批。

## 验证

写完文档后，回读新文档文件，并检查请求路径是否存在。
