# 当前动画映射

## 核心动画基础

| 文件 | 当前作用 | 重要位置 | 800/600 依赖 |
| --- | --- | --- | --- |
| `system/Sprite.tjs` | 基础异步精灵动画层，处理透明度、移动、样条移动、缩放、旋转和转场完成回调。 | `setBlendingEnvelope`、`setMovingEnvelope`、`setMovingSplineEnvelope2`、`beginActivation`、`on_activationTimer`、`beginTransition` | 没有直接 `800x600` 常量。应继续作为通用层。 |
| `system/AnimationSequence.tjs` | `.ams` 序列运行器，处理 setup 和 animation 标签，创建 `AnimationSprite` 并执行路径动画。 | `set`、`setmulti`、`show`、`hide`、`pos`、`motion`、`particle`、`wait` 等标签处理 | 当前文件没有直接 `800x600` 常量，但 `.ams` 数据传入的路径和基准坐标可能是旧坐标。 |
| `system/Action.tjs` | 可复用属性动作框架，驱动线性、波形、随机、震动、移动、淡入淡出、缩放波动等动作。 | `ActionPropBase`、`LinearAction`、`WaveAction`、`RandomAction`、`ActionMove`、`ActionWave`、`ActionShock`、ADV 移动/淡入淡出辅助 | 大多与坐标无关。宽高和移动距离由调用方传入，旧值可能来自场景或 UI 调用。 |
| `system/EnvEffect.tjs` | 环境动画，如雪、雨、粒子式环境效果。 | 定时器、随机生成区域、逐帧绘制 | 部分依赖尺寸。部分代码使用 `WINDOW_WIDTH` / `WINDOW_HEIGHT`，但雪/雨仍包含 `1280x720` 时代的生成范围和固定偏移。 |

## UI 动画调用点

| UI 区域 | 文件 | 当前行为 | 当前坐标和旧基准依赖 |
| --- | --- | --- | --- |
| 标题启动淡出 | `system/Title.tjs` | 白色遮罩通过 `SPR_COVER.setBlendingEnvelope(0)` 和 `beginActivation(1000)` 在 `1000ms` 内淡出。 | 遮罩使用全精灵尺寸，但标题图、Logo、菜单仍是旧布局。 |
| 标题菜单出现 | `system/Title.tjs` | `_grpBtn` 在 `fadeEnd()` 中从透明度 `0` 淡入到 `255`，耗时 `500ms`。 | `_btnBase` 固定 `800x90`，菜单行贴 `WINDOW_HEIGHT - 90`，主菜单从 `x=89,y=24` 开始，Bonus 行在 `x=434,y=36`。 |
| 标题 Logo | `system/Title.tjs` | 当前为静态可见精灵，没有独立入场动画。 | Logo 固定在 `176,88`，属于旧 800 宽构图。 |
| 标题开始退出 | `system/Title.tjs` | 主标题层 `3000ms` 淡出，若干精灵淡出/移动/缩放。 | 退出偏移 `[64,-64,128,-128]` 是为旧标题图间距设计。 |
| 标题 Bonus 切换 | `system/Title.tjs` | 主菜单和 Bonus 菜单交叉淡入淡出，并在 `300ms` 内竖向移动 `32px`。 | 使用旧 `_btnBase` 区域和旧行坐标。`32px` 在 1080p 下可能偏小。 |
| 自动存档悬停预览 | `system/Title.tjs` | 预览从 `y-16` 移动到 `y` 并淡入，耗时 `300ms`。 | 位置由旧标题按钮行计算，`16px` 是旧尺度。 |
| 对话框显示/隐藏 | `system/MessageFrame.tjs` | 整个 `MessageFrame` 以请求时长淡入或淡出；`showMessage` / `hideMessage` 淡入淡出 `_base`。 | 对话框常量是旧/部分 HD 混合：宽 `750`、高 `135`、底部锚点、关闭/语音按钮旧位置、novel 模式 `1280x720`。 |
| 对话框类型切换 | `system/MessageFrame.tjs` | 旧消息底板淡出，新底板淡入，默认 `300ms`。 | 底板和位置使用旧坐标：`BASE_X=25`、`BASE_Y=FRAME_Y-33`、闪烁位置约 `743`、关闭位置 `750`。 |
| 对话光标闪烁 | `system/MessageFrame.tjs` | 闪烁图标 `300ms` 淡入，定时器每 `4500ms` 重复。 | 默认闪烁位置依赖旧框架图；运行时文字光标定位更可靠，但仍依赖消息区域布局。 |
| 系统菜单区域 | `system/MessageFrame.tjs` | `SystemMenuArea.enter()` 滑到 `y=0`；`leave()` 滑到 `y=48`，耗时 `150ms`。 | 菜单是 `800x33`；按钮位置从 `58+0` 到 `58+625`；默认底部位置使用 `WINDOW_HEIGHT - 33`，但宽度是旧的。 |
| 角色脸图窗口 | `system/MessageFrame.tjs` | 显示时淡入并从左向右滑动 `16px`；隐藏时向右滑动 `16px`。 | 脸图锚点 `182 - width/2`，底部 `height - face.height - 8`，是旧对话框构图。 |
| 快速存档子菜单 | `system/MessageFrame.tjs` + `SelectItem.tjs` | 通过 `SubMenu.show()` 立即显示；快速存档列表没有入场动画。 | 子菜单会按 `WINDOW_WIDTH` / `WINDOW_HEIGHT` 限制屏幕边界，但素材和列表坐标仍旧。 |
| 选项基础控件 | `system/SelectItem.tjs` | 按钮悬停/按下即时切换图片状态；滑条拖动即时更新裁切位置；子菜单即时显示/隐藏。 | 导航和边界限制使用 `WINDOW_WIDTH` / `WINDOW_HEIGHT`；单个按钮依赖调用方布局和旧素材。 |
| 规则转场 | `rule/*` | PNG 遮罩用于全屏擦除、帘幕、快门、笔刷等转场。 | 这些是资源，不是代码。用于 1920x1080 全屏转场前必须视觉检查。 |

## 需要标记的旧尺寸依赖

高风险硬布局值：

- `Title.tjs`：`_btnBase.setSize(800, 90)`，标题菜单从 `89,24` 和 `434,36` 开始，Logo 在 `176,88`。
- `Title.tjs`：Bonus 切换竖向偏移 `32`，自动存档预览偏移 `16`，开始退出偏移 `64` 和 `128`。
- `MessageFrame.tjs`：`FRAME_WIDTH=750`、`FRAME_HEIGHT=135`、`BASE_X=25`、旧按钮/闪烁位置、关闭位置 `750`、系统菜单 `800x33`。
- `MessageFrame.tjs`：`FRAMELIST` 的 novel 模式使用 `w:1280, h:720`。
- `MessageFrame.tjs`：脸图锚点 `182` 和侧向滑动 `16`。
- `EnvEffect.tjs`：雪 `_generateArea = "0,0,1280,720"`，雪随机生成矩形接近 `1380`，雨生成宽度 `1680`。

低风险值：

- `150`、`300`、`500`、`1000`、`3000`、`5000`、`8000` 多数是动画时长，不是分辨率坐标。
- `0`、`96`、`128`、`255` 是透明度或状态值。
- 点击阈值 `0` 和 `256` 是交互遮罩阈值，不是位置。

## 现有优势

- 大多数 UI 定时动画已经复用同一套 `Sprite` envelope API。
- 基础动画引擎与坐标无关，不需要为了 `1920x1080` 替换。
- `SubMenu.show()` 已经按 `WINDOW_WIDTH` / `WINDOW_HEIGHT` 限制弹窗边界，对迁移有帮助。
- `Action.tjs` 集中了常见运动行为，新 UI 动画可以复用现有 easing，不必新增逐帧系统。

## 现有缺口

- 没有统一的 UI 动画策略，缺少时长、缓动、位移距离标准。
- 悬停和子菜单多数是瞬间切换，1080p 下会显得生硬。
- 部分菜单动画手动禁用点击区域，较长或错开的动画不能导致控件一直不可点击。
- 旧尺度位移在 1080p 下可能太小，但直接按比例放大又可能太夸张。
