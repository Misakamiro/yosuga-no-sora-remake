# 性能测试计划

## 静态检查

任何性能改动后运行：

```powershell
rg -n "new Timer|interval =|\\.interval|enabled = true|enabled = false|addContinuousHandler|removeContinuousHandler" startup.tjs k2compat.tjs system/Window.tjs system/ADVScreen.tjs system/Sound.tjs system/Sprite.tjs system/AnimationSequence.tjs system/EnvEffect.tjs
```

```powershell
rg -n "new global\\.Layer|new Layer|invalidate|assignImages|loadImages|setImageSize|setSizeToImageSize|piledCopy|copyRect|affineCopy" system/ADVScreen.tjs system/Sprite.tjs system/AnimationSequence.tjs system/EnvEffect.tjs system/Window.tjs
```

```powershell
rg -n "SoundBuffer|WaveSoundBuffer|PlayBgm|StopBgm|PlaySe|StopSe|PlayEnvSe|StopEnvSe|PlayVoice|StopVoice|PlayBgv|StopBgv" system/Sound.tjs system/ADVScreen.tjs k2compat.tjs
```

```powershell
rg -n "WINDOW_WIDTH|WINDOW_HEIGHT|1920|1080|800|600|piledCopy|fillRect|affineCopy" system/ADVScreen.tjs system/EnvEffect.tjs system/Window.tjs system/Sprite.tjs system/AnimationSequence.tjs
```

期望：

- 每个新 timer 都有对应停止/finalize 路径。
- 每个新 continuous handler 都有对应 remove 路径。
- 临时 layer 会 invalidate。
- 如果加入相同文件图片缓存，不会跳过必要的 layer 重置。
- 音频 buffer 仍通过原有公开 API 停止/淡出。

## 运行时基线

使用 Windows 任务管理器、资源监视器、Process Explorer 或可重复脚本测量。记录 CPU、内存和视觉表现。

基线场景：

- 启动到标题并待机 60 秒。
- 开始普通 ADV 场景并待机 60 秒。
- 触发带转场的背景切换。
- 触发角色显示/隐藏转场。
- 触发自动模式和跳过模式。
- 触发雪效果 60 秒。
- 触发雨效果 60 秒。
- 触发背景滚动效果 60 秒。
- 播放 BGM、切换 BGM、播放语音、播放 SE、播放 ENVSE/BGV，然后分别停止/淡出。
- 打开存档/读档/历史，并创建一次存档缩略图。
- 切换窗口/全屏并缩放窗口。

记录：

- 平均 CPU。
- 转场/效果期间峰值 CPU。
- Working set / private memory。
- 停止效果和音频后，内存是否接近回落基线。
- 视觉流畅度和明显卡顿。
- 雨/雪/背景滚动是否在场景清理后停止。
- 音频淡出是否完成，buffer 是否停止。

## 每次优化后的回归检查

### Timer 和动画

- 反复启动/停止雪和雨。
- 环境效果活动时切换场景。
- 反复启动动作/表情动画。
- 确认 `stopEnvEffect` 后没有可见效果继续运行。
- 确认场景 action 在场景清理/finalize 后不继续运行。

### Layer 和图片加载

- 同一背景显示两次。
- 不同背景交叉淡入。
- 同一角色 id 切换不同表情。
- 显示和移除 cutin/flash。
- 打开选项并关闭。
- 确认旧转场 layer 在转场后消失。

### 音频

- 重复播放同一个 SE。
- BGM 交叉淡入到另一曲。
- BGM 淡出停止和立即停止。
- 自动模式下播放语音。
- 播放/停止 ENVSE 和 BGV 循环。
- 确认跳转、读档、视频后没有重复环境音残留。

### 1920x1080 准备度

只在分辨率模块开始实现后运行：

- 在 `1920x1080` 下重复基线场景。
- 对比全画布转场成本和 `800x600` 的差异。
- 确认存档缩略图不会过慢或过大。
- 确认雨效果填充和背景滚动在 1080p 下成本可接受。
- 连续 5 次场景切换后，确认内存不持续增长。

## 通过标准

- 启动、ADV、效果、存档/读档、音频流程无崩溃或脚本错误。
- 效果/action 停止后，空闲 CPU 不持续偏高。
- 反复场景/效果/音频循环后，内存不持续增长。
- 雨、雪、转场、角色动作视觉时序可接受。
- 音频淡出/交叉淡入行为保持不变。
- 性能优化批次不要求修改图片资源、剧本文件或封包文件。
