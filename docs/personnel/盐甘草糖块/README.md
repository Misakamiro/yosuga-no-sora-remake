# 盐甘草糖块责任包

## 职责定位

盐甘草糖块负责代码方向，主负责 `resolution-1920x1080`、`performance`，协助 `scenario-parser`、`ui-redesign`。

核心目标：

- 将运行逻辑和页面坐标逐步适配到 `1920x1080`。
- 建立性能基线，避免 1080p 大画布、动画和环境效果造成明显卡顿。
- 配合剧本和 UI 人员处理选项、消息框、系统页面位置。

## 优先阅读顺序

1. `00-project-overview.md`
2. `01-file-module-split.md`
3. `modules/resolution-1920x1080/README.md`
4. `modules/resolution-1920x1080/current-800x600-map.md`
5. `modules/resolution-1920x1080/migration-plan.md`
6. `modules/performance/README.md`
7. `modules/performance/current-risk-list.md`
8. `modules/ui-redesign/layout-standard.md`

## 模块目录

- `modules/resolution-1920x1080`
- `modules/performance`
- `modules/scenario-parser`
- `modules/ui-redesign`

## 主要负责路径

- `system/Window.tjs`
- `system/SystemWindow.tjs`
- `system/ADVScreen.tjs`
- `system/ADVObject.tjs`
- `system/MessageArea.tjs`
- `system/MessageFrame.tjs`
- `system/SetupADVObject.tjs`
- `system/SelectItem.tjs`
- `system/ConfigWindow.tjs`
- `system/Album.tjs`
- `system/GameSceneManager.tjs`
- `system/AnimationSequence.tjs`
- `system/Action.tjs`
- `system/Sound.tjs`
- `system/Movie.tjs`
- `system/Sprite.tjs`
- `system/Utility.tjs`
- `system/bgData.csv`
- `bgData.csv`
- `tvpwin32.cf`

## 禁止事项

- 不直接重绘或替换图像。
- 不修改剧情语义。
- 不用删除动画、环境效果或资源的方式掩盖性能问题。
- 不把临时比例缩放当作最终 16:9 适配。

## 交付要求

- 每个坐标修改都写旧值、新值、影响页面。
- 性能优化必须有修改前后基线。
- 涉及选项、消息框和名称栏时通知 yuuki。
- 涉及 UI 页面结构时通知凝言。
