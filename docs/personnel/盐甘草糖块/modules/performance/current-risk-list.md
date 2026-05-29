# 当前性能风险清单

## 范围和证据

本清单基于已分配文件和只读辅助检查。这里记录的是风险，不是已确认缺陷。标注“需要运行时证明”的项目，必须先测量再改代码。

## 高风险

### 环境效果使用短重绘间隔

证据：

- `system/EnvEffect.tjs`：`EnvEffectBase` 创建 `_envEffectTimer`，`interval = 20`。
- 雪和雨重写为 `interval = 30`。
- 雪还有 `_generateTimer`，默认 `200ms`；脚本可设 `generateinterval`，在 `set` 中默认 `100ms`。
- 雨每隔一次绘制会调用 `_base.fillRect(...)`，并遍历所有雨滴。
- 背景滚动每次绘制都会用嵌套循环平铺源层。

风险：

- `20ms` 约等于每秒 50 次回调，`30ms` 约等于每秒 33 次。
- `800x600` 下很多场景可接受，但 `1920x1080` 会明显增加全屏填充/复制成本。
- 多个环境效果、ADV 转场、连续 action 回调可能叠加。

状态：

- 已确认是风险。需要运行基线来决定是否调整默认间隔、暂停不可见效果或限制数量。

### 动画/action 期间连续 handler 可能每帧运行

证据：

- `system/Sprite.tjs` 使用 `System.addContinuousHandler(on_activationTimer)` 处理透明度、移动、缩放、旋转 envelope。
- `system/ADVScreen.tjs` 使用 `System.addContinuousHandler(onActionCallback)` 处理场景 action。
- `system/AnimationSequence.tjs` 的动画精灵在 finalize/stop 路径中移除连续 handler。

风险：

- 连续 handler 对顺滑动画是必要的，但多个精灵同时活动或 stop/finalize 漏掉时会变重。
- `Sprite.on_activationTimer` 在缩放时每次回调都可能重新计算插值辅助状态。
- action、转场和环境效果 timer 可能同时叠加。

状态：

- 已确认是设计风险。本轮没有证明存在直接泄漏。

### `1920x1080` 下全屏转场复制成本会放大

证据：

- `system/ADVScreen.tjs` 在 `UPDATE_CG` 时用 `piledCopy(0, 0, this, 0, 0, WINDOW_WIDTH, WINDOW_HEIGHT)` 把当前场景复制到 `_sprTrans`。
- 全屏色闪和兜底填充使用 `WINDOW_WIDTH`、`WINDOW_HEIGHT`。
- `system/Window.tjs` 的主层/底层按逻辑窗口尺寸创建。

风险：

- 当前 `system/Status.tjs` 仍是 `800x600`，但迁移文档已经计划 `1920x1080`。
- `1920x1080` 全屏转场缓冲像素量约为 `800x600` 的 4.32 倍。
- 交叉淡入或 universal 转场可能把复制成本和图片解码/设置成本叠加。

状态：

- 已确认是未来高风险。改逻辑分辨率前后都要测量。

## 中风险

### 重复图片加载路径需要检查缓存策略

证据：

- `system/Sprite.tjs` 在 `loadImages(file)` 后保存 `_file`，但没有用它跳过相同文件重载。
- `system/AnimationSequence.tjs` 在 `loadImage` 中加载资源，随后精灵使用 `assignImages(refImage.image)`，同一序列内复用较好。
- `system/EnvEffectSnow.create` 每次雪开始时加载 4 张雪图，停止时销毁。
- `system/EnvEffectBgScroll.setParam` 每次设置参数都加载滚动图。
- `system/ADVScreen.tjs` 的背景/角色设置可能替换层并触发转场。

风险：

- 部分路径已通过 `assignImages` 避免重复像素复制。
- 包装层没有明显的相同文件跳过逻辑，因此连续剧本标签可能仍触发额外加载，具体取决于引擎缓存。
- 雪效果每次开始/停止都会销毁和重载小粒子图，内存不大但可能有抖动。

状态：

- 需要场景 trace 或加载耗时证明后，再考虑脚本级缓存。

### 音频层依赖周期清理和淡出完成

证据：

- `system/Sound.tjs` 的 `SoundLayer` 启动 `1000ms` `_cleaningTimer`。
- `cleaning()` 删除 `destroy` 为 true 的 buffer。
- BGM 有意使用 `BGM_1` 和 `BGM_2` 交替实现交叉淡入。
- SE、ENVSE、BGV 常规路径会按 id/file 停止后再播放。
- `ADVScreen.finalize` 会 invalidate `VOICE` 和 `BGV`；BGM/SE/ENVSE 是更全局的层。

风险：

- 每秒清理一次 CPU 成本不高，但已停止/淡出的 buffer 可能等到标记 destroy 并清理才释放。
- 长淡出和循环 ENVSE/BGV 如果 stop 标签漏掉，可能长期存活。
- BGM 双缓冲是预期行为，但转场期间会看起来有两个活动 buffer。

状态：

- 中风险，静态检查未确认泄漏。

### 调试输出在配置/启动中启用

证据：

- `tvpwin32.cf` 有 `debugwin="yes"`。
- `startup.tjs` 启动时输出 `Debug.message`。
- 部分脚本还有额外 debug message 和 notice。

风险：

- 调试窗口/日志会增加启动和错误路径噪音。
- 当前兼容还不稳定，调试信息仍有价值，不应作为第一优化点。

状态：

- 已确认配置风险。等启动兼容稳定后，可在 release/performance 构建中关闭。

## 低风险或当前可控

### 鼠标 timer 大多受限

证据：

- `system/Window.tjs` 鼠标自动隐藏 timer 每 `1000ms` 执行。
- 鼠标跟踪 timer 是 `10ms`，但只在光标跟踪期间启用，到达目标后会关闭。

风险：

- `10ms` 频率高，但不是常开。

状态：

- 除非 UI 流程持续触发鼠标跟踪，否则风险较低。

### AnimationSequence 有显式清理路径

证据：

- `AnimationSequence.finalize` 会 invalidate controller、sprites、images。
- `AnimationSequence.clear` 会 invalidate sprite images。
- `AnimationSequenceList.clean` 删除 `isCanDestroy()` 为 true 的序列。
- `AnimationSequenceList.removeOfParent` 可按 parent 删除序列。

风险：

- 清理路径存在，但正确性取决于 end/stop 是否触发。
- 需要在反复 emotion/action 标签场景中测试。

状态：

- 当前可控，但需要运行时回归测试。

## 1920x1080 大图加载风险

当前状态：

- `system/Status.tjs` 仍定义 `800x600` 画布常量。
- `docs/modules/resolution-1920x1080` 已有迁移计划。
- 只读资源清单中最大的 TLG 压缩文件约 1.4MB。压缩大小不能证明解码后大小。

风险：

- 一张 `1920x1080` 32 位解码层约 7.9MB，不含额外转场/源/目标缓冲。
- 一个场景可能同时持有背景、转场复制、角色层、效果底层、事件相机、临时存档/截图层。
- 全屏雨、背景滚动、闪白和转场复制会在画布变大后成为主要风险。

状态：

- 按未来高风险处理。不要在同一批改动里同时迁移分辨率和改性能行为。

## 当前不建议修改

- 不要在没有视觉对比场景前降低环境效果间隔或粒子数。
- 不要在没有重复加载证据前添加全局图片缓存，KRKR 可能已在 storage/layer 层缓存。
- 不要移除 BGM 双缓冲交叉淡入。
- 启动兼容问题仍在调查前，不要关闭所有 debug 输出。
- 不要把 `1920x1080` 常量变更混入性能文档任务。
