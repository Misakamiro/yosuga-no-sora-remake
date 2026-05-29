# 当前 800x600 映射

## 扫描范围

本轮只扫描已分配文件，重点搜索：

`WINDOW_WIDTH`、`WINDOW_HEIGHT`、`800`、`600`、`400`、`300`，并额外查看附近的 `1280` 和 `720`，因为部分 UI 文件里已经存在类似 HD 的常量。

命中统计：

| 文件 | 相关命中数 | 主要分类 |
|---|---:|---|
| `system/Status.tjs` | 5 | 逻辑分辨率常量 |
| `system/Window.tjs` | 26 | 窗口、客户端区、全屏缩放 |
| `system/ADVScreen.tjs` | 48 | ADV 画布、CG 中心、选项、电影遮幅 |
| `system/ADVObject.tjs` | 3 | ADV 对象画布和全屏色块 |
| `system/MessageFrame.tjs` | 20 | 对话 UI 布局和系统菜单点击区 |
| `system/MessageArea.tjs` | 0 | 本轮没有直接命中宽高字面量 |
| `system/Movie.tjs` | 3 | 视频播放层尺寸 |
| `system/SystemWindow.tjs` | 17 | 存档/读档/历史叠层、预览、文字边距 |
| `system/ConfigWindow.tjs` | 15 | 设置叠层、画面尺寸选项、提示框边界 |
| `system/Album.tjs` | 30 | CG 浏览器、预览、截图缩略图源 |
| `system/Title.tjs` | 12 | 标题、Logo 位置和底部按钮层 |

## 逻辑分辨率

### `system/Status.tjs`

- 第 68-72 行定义 `WINDOW_WIDTH = 800`、`WINDOW_HEIGHT = 600`、`WINDOW_ASPECT`、`WINDOW_CENTER_X`、`WINDOW_CENTER_Y`。
- 分类：逻辑分辨率根节点。
- `1920x1080` 处理：第一阶段候选修改点。
- 注意：代码里的宽高比是高度除以宽度，4:3 时为 `0.75`，16:9 后为 `0.5625`，所有依赖 `WINDOW_ASPECT` 的布局都要重新测试。

### `system/Window.tjs`

- 构造函数使用 `setInnerSize(WINDOW_WIDTH, WINDOW_HEIGHT)`，并按逻辑尺寸创建 `primaryLayer` / `baseLayer`。
- 全屏使用 `AdjustFitSize(System.screenWidth, System.screenHeight, WINDOW_WIDTH, WINDOW_HEIGHT)`。
- 缩放逻辑依赖 `WINDOW_WIDTH`、`WINDOW_HEIGHT`、`WINDOW_ASPECT` 保持比例。
- 分类：逻辑视口和物理窗口缩放。
- `1920x1080` 处理：第一阶段候选修改点，但必须完整测试窗口和全屏流程。

## ADV 画布、CG 和图片中心

### `system/ADVScreen.tjs`

- `CUTIN_X = WINDOW_WIDTH \ 2`、`CUTIN_Y = WINDOW_HEIGHT \ 2 - 100`：切入图中心与垂直偏移。水平中心可跟随常量，`-100` 是演出偏移，需视觉确认。
- ADV 根层、转场精灵、背景对象、黑底填充使用 `WINDOW_WIDTH`、`WINDOW_HEIGHT`、`WINDOW_CENTER_X`、`WINDOW_CENTER_Y`：可跟随常量。
- 默认 `.setCenter(400, 300)`：这是旧画面中心。只有确认该路径表示“屏幕中心”时，才替换为 `WINDOW_CENTER_X`、`WINDOW_CENTER_Y`。
- `BASE_POS` 中的 `-400`、`-300`、`300`、`400` 是角色自动站位偏移，不是单纯分辨率值。需要 16:9 角色合成检查后再改。
- 选项列表里 `cy = 300 - ...` 以旧垂直中心排列选项。可在测试后改成 `WINDOW_CENTER_Y`，但按钮素材仍是旧尺寸。
- 电影遮幅 `STRAP_WIDTH = 1280`、`STRAP_HEIGHT = 128` 是遮幅资源尺寸。`1920x1080` 下应覆盖 1920 或改用 `WINDOW_WIDTH`，但要等素材。

### `system/ADVObject.tjs`

- 构造函数 `setSize(WINDOW_WIDTH, WINDOW_HEIGHT)`。
- 色块/图片兜底使用 `_spr.setImageSize(WINDOW_WIDTH, WINDOW_HEIGHT)` 并填满画布。
- 分类：ADV 对象画布。
- 第一阶段可通过常量传播。

## 对话 UI

### `system/MessageFrame.tjs`

- `FRAME_WIDTH = 750`、`FRAME_HEIGHT = 135`、`FRAME_Y = WINDOW_HEIGHT - FRAME_HEIGHT` 属于旧对话框尺寸。
- `BASE_X = 25`、`BASE_Y = FRAME_Y - BASE_MOVE_H`、`BTN_BASE_Y1 = WINDOW_HEIGHT - 33`、`BTN_BASE_Y2 = 684` 是旧 UI 坐标和混合坐标。
- `HIDE_X1`、`HIDE_Y1`、`HIDE_X2`、`HIDE_Y2`、`BLINK_X`、`BLINK_Y` 涉及隐藏/关闭/闪烁提示位置。
- `FRAMELIST` 中存在 `%[type:"nobel", w:1280, h:720]`，这是部分 HD 模式，不等于最终目标。
- 名字区域和正文区域仍按旧尺寸设置。
- 系统菜单层尺寸为 `800x33`。
- 分类：UI 坐标、点击区域、旧素材尺寸和部分 HD 尝试。
- 处理：绝大多数变更等待新 UI 素材和布局，不要第一阶段盲改。

### `system/MessageArea.tjs`

- 本轮扫描没有直接命中旧宽高。
- 分类：文字排版引擎。
- 处理：除非新对话框尺寸暴露换行问题，否则第一阶段不动。

## 视频播放

### `system/Movie.tjs`

- 第 11、14 行把视频层设置为 `800x600`。
- 第 16 行在 `WINDOW_WIDTH`、`WINDOW_HEIGHT` 内居中。
- 分类：视频播放区域。
- 决策：
  - 如果视频源仍是 `800x600`，保留源尺寸并在 `1920x1080` 中居中或加设计框。
  - 如果视频已重制，改为 `1920x1080` 或使用按源尺寸适配的辅助函数。
- 第一阶段建议：没有新视频素材前保留旧源尺寸策略。

## 存档、读档、历史和缩略图

### `system/SystemWindow.tjs`

- 存档/读档叠层使用 `WINDOW_WIDTH`、`WINDOW_HEIGHT`。
- 历史界面底边距和尺寸使用 `WINDOW_HEIGHT`。
- 历史预览宽度使用 `1280 * (h / WINDOW_HEIGHT)`，这个 `1280` 可疑，需要确认预览图比例。
- 预览边界用 `new Rect(0, 0, WINDOW_WIDTH, WINDOW_HEIGHT)`。
- 历史消息边距使用 `WINDOW_WIDTH-100`、`WINDOW_HEIGHT`，在 1080p 下可能过宽。
- 分类：叠层画布、历史预览缩放、文字视口。
- 第一阶段：全窗口常量可跟随，预览比例需要单独确认。

## 设置窗口

### `system/ConfigWindow.tjs`

- 设置叠层使用 `WINDOW_WIDTH`、`WINDOW_HEIGHT`。
- 全局设置底图显式使用 `1280x720`。
- 画面尺寸选项仍是 4:3：`1600x1200`、`1200x900`、`800x600`、`600x450`、`400x300`。
- 提示弹窗按 `WINDOW_WIDTH`、`WINDOW_HEIGHT` 限制。
- 分类：UI 素材、设置选项、提示边界混合。
- 第一阶段：叠层和提示边界可跟随常量；画面尺寸选项需要产品决策和 UI 文案资源。

## Album 和 CG 浏览

### `system/Album.tjs`

- Album 场景使用全窗口常量。
- 查看菜单是 `WINDOW_WIDTH x 306`，底部点击区为 156px。
- 缩放视图区域按逻辑屏幕 50% 计算，并以 `WINDOW_CENTER_X/Y` 居中。
- 缩略图源复制 `WINDOW_WIDTH x WINDOW_HEIGHT`。
- CG 大于逻辑窗口时显示横向/纵向滑条。
- 裁切数学拿图片尺寸和 `WINDOW_WIDTH`、`WINDOW_HEIGHT` 比较。
- 分类：CG 视口、缩略图源、底部 UI 点击区域。
- 第一阶段：画布和 CG 数学可以跟随常量，菜单/控制坐标等待 UI 素材。

## 标题和 Logo

### `system/Title.tjs`

- Logo 注意事项精灵使用旧中心和旧位置。
- Logo/视频场景使用全窗口常量。
- 标题按钮底层是 `800x90`，底部对齐。
- 分类：标题 UI 坐标和图片中心。
- 处理：标题素材和布局重做前不要移动按钮。全场景画布可跟随常量，但可见素材会保持旧尺寸。

## 不应当视为分辨率的值

- 大多数 `300` 是动画时长，例如 `beginActivation(300)`、`show(300)`、`hide(300)`、`ReviceEffectSpeed(300)`。
- 部分 `400`、`300`、`600`、`800` 是 UI 位置、按钮素材尺寸、文本框宽度或旧画面尺寸预设。
- `MessageFrame.tjs` 和 `ConfigWindow.tjs` 中的 `1280x720` 是现存 UI/素材尺寸，不是最终目标分辨率。
