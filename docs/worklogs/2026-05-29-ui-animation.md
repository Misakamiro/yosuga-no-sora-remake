# 2026-05-29 UI 动画工作日志

## 范围

任务：记录当前 UI 动画系统、标记旧坐标依赖，并准备 `1920x1080` 动画升级计划。

项目路径：

`C:\Users\misakamiro\Downloads\Compressed\yosuga_krkrZ\yosuga_krkrZ`

分配文件：

- `system/Sprite.tjs`
- `system/AnimationSequence.tjs`
- `system/Action.tjs`
- `system/EnvEffect.tjs`
- `system/MessageFrame.tjs`
- `system/SelectItem.tjs`
- `system/Title.tjs`
- `rule/*`

## 已执行动作

1. 确认项目路径存在。
2. 确认分配的系统文件和 `rule/` 资源存在。
3. 检查仓库状态，确认该项目路径不是 Git 仓库。
4. 扫描分配文件中的动画术语、`WINDOW_WIDTH`、`WINDOW_HEIGHT`、`800`、`600`、`1280`、`720`、位置/尺寸调用、透明度、转场和规则遮罩引用。
5. 阅读主要实现区域：
   - `Sprite.tjs` 异步 activation 和转场完成。
   - `AnimationSequence.tjs` 的 `.ams` 标签、motion、particle 和 `AnimationSprite`。
   - `Action.tjs` 的动作基础和可复用 ADV/UI 运动辅助。
   - `MessageFrame.tjs` 的对话框显示/隐藏、消息类型淡入、闪烁、系统菜单、脸图窗口、快捷菜单。
   - `SelectItem.tjs` 的按钮/组/子菜单交互基础。
   - `Title.tjs` 的标题菜单布局、标题淡入、开始退出、自动存档预览、Bonus 菜单切换。
   - `EnvEffect.tjs` 的环境动画 timer 和旧尺寸生成范围。
   - `rule/*` 转场遮罩清单。
6. 创建 UI 动画交接文档。

## 已创建文件

- `docs/modules/ui-animation/README.md`
- `docs/modules/ui-animation/current-animation-map.md`
- `docs/modules/ui-animation/animation-upgrade-plan.md`
- `docs/modules/ui-animation/performance-risk.md`
- `docs/worklogs/2026-05-29-ui-animation.md`

## 关键发现

- `system/Sprite.tjs` 是当前共享 UI 动画基础，应保留。
- `system/AnimationSequence.tjs` 和 `system/Action.tjs` 大多与坐标无关，但会从调用方或序列文件接收旧坐标。
- `system/Title.tjs` 包含旧标题布局和 800 宽 UI 相关的动画距离。
- `system/MessageFrame.tjs` 是 UI 动画和旧坐标依赖最密集的文件，包括对话框常量、系统菜单宽度、按钮位置、闪烁位置、脸图位置和部分 `1280x720` novel 模式。
- `system/SelectItem.tjs` 大多即时切换状态，未来适合做子菜单容器动画，但第一版不应全局给每个按钮加动画。
- `system/EnvEffect.tjs` 不属于普通 UI 动画，但它有连续 timer 和 1280/1680 生成范围假设，`1920x1080` 下会影响性能。
- `rule/*` 是转场遮罩，应当作为全屏场景转场资源，而不是小 UI 动画资源。

## 推荐下一步

等待 `1920x1080` UI 布局和资源确定最终坐标。之后按窄范围实施：

1. 标题菜单/分组入场和 Bonus 菜单切换。
2. 对话框显示/隐藏和类型切换。
3. 系统菜单/子菜单显示。
4. 选项组出现。
5. 设置/存档/读档窗口级转场，需这些模块另行分配后再做。

## 验证状态

已完成：

- 分配文件静态扫描。
- 当前动画位置人工分类。
- 旧坐标依赖人工分类。
- 文档创建。

未执行：

- 游戏运行启动。
- 截图对比。
- 代码修改。
- 资源修改。
- 打包。

原因：本任务只要求先提供动画方案，没有要求直接做大代码改动。
