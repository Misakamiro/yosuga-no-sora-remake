# UI 动画模块交接

## 目标

为 `1920x1080` UI 重做准备动画方案。目标不是重写动画系统，而是在现有 KRKRZ 脚本模型上做低风险升级：保留 `Sprite` 的淡入、位移、缩放、旋转和转场完成回调，只在必要的 UI 组上增加更流畅的显示/隐藏动画。

## 已分配范围

本模块只分析和规划以下文件/资源：

- `system/Sprite.tjs`
- `system/AnimationSequence.tjs`
- `system/Action.tjs`
- `system/EnvEffect.tjs`
- `system/MessageFrame.tjs`
- `system/SelectItem.tjs`
- `system/Title.tjs`
- `rule/*`

不处理：

- 引擎启动兼容。
- 剧本解析和选项跳转。
- 原始背景、CG、立绘、UI 图片资源本身。
- 分辨率核心常量。
- 打包发布。

## 当前结论

- `system/Sprite.tjs` 是当前最重要的通用动画基础，应继续复用。
- `AnimationSequence.tjs` 和 `Action.tjs` 大多与分辨率无关，但传入的路径、坐标、序列数据可能仍是旧坐标。
- `Title.tjs`、`MessageFrame.tjs`、`SelectItem.tjs` 是 UI 动画改造的主要入口。
- `EnvEffect.tjs` 属于环境效果，不是普通 UI 动画，但它的高频定时器会影响 `1920x1080` 性能，需要在动画升级时避开叠加风险。
- `rule/*` 是全屏转场遮罩，适合场景切换，不适合普通按钮、菜单、设置页动画。

## 文档索引

- `current-animation-map.md`：当前动画调用点和旧坐标依赖。
- `animation-upgrade-plan.md`：`1920x1080` UI 动画升级计划。
- `performance-risk.md`：动画升级的性能风险和限制。
- `../../worklogs/2026-05-29-ui-animation.md`：本轮工作日志。

## 推荐执行顺序

1. 等 UI 布局和素材确认最终坐标。
2. 先做标题菜单组进入和 Bonus 菜单切换。
3. 再做对话框显示/隐藏和对话框类型切换。
4. 再做系统菜单/子菜单显示。
5. 最后做选项组出现、设置/存档/读档窗口级动画。

每一步都要验证快速点击、跳过模式、关闭动画和点击区域恢复。
