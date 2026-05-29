# 启动链记录

## 目标启动流程

当前移植层希望按以下顺序启动：

```text
startup.tjs
  -> k2compat.tjs
  -> Status.tjs
  -> Initialize.tjs
  -> begin.tjs
```

## 根目录 startup.tjs

根目录 `startup.tjs` 当前做了这些事：

1. 覆盖 `System.inform`，把弹窗消息写入 `Debug.message`。
2. 执行 `Scripts.execStorage("k2compat.tjs")`。
3. 注册自动路径：
   - `system/`
   - `content-data/`
4. 加载 `Status.tjs`。
5. 加载 `Initialize.tjs`。
6. 加载 `begin.tjs`。

关键点：根目录版本会打印 `Auto-paths: system/ and content-data/ registered`。

## content-data/startup.tjs

`content-data/startup.tjs` 也存在，而且和日志更匹配。它做了这些事：

1. 覆盖 `System.inform`。
2. 执行 `Scripts.execStorage("k2compat.tjs")`。
3. 只注册 `system/`。
4. 加载 `Status.tjs`。
5. 加载 `Initialize.tjs`。
6. 打印 `=== Starting begin.tjs ===` 后加载 `begin.tjs`。

关键点：日志中的 `Auto-paths: system/ registered` 和 `=== Starting begin.tjs ===` 都来自这个版本，而不是根目录 `startup.tjs` 的最新文本。

## Status.tjs

`Status.tjs` 定义基础常量和编译开关：

- `__DEBUGMODE__ = 0`
- `__TRIAL__ = 0`
- `__FILE_CRYPT__ = 1`
- `__SPECIAL__ = 1`
- `__HARUKA__ = 0`
- 游戏标题、窗口尺寸、保存版本、参数上限、`SELECTTERMINATE_FLAG` 等。

`Initialize.tjs` 依赖这里的 `GAME_CAPTION`、窗口常量和系统常量。

## Initialize.tjs

`system/Initialize.tjs` 是主系统初始化入口：

1. 检查多重启动：`System.createAppLock("CuffsApplication-"+GAME_CAPTION)`。
2. 注册资源搜索路径，包括 `system/`、`scenario/`、`plugin/` 等。
3. 加载插件：
   - `extrans.dll`
   - `csvParser.dll`
   - `windowEx.dll`
   - `layerExDraw.dll`
   - `fstat.dll`
   - `KAGParserEx.dll`
4. 加载系统脚本，重点顺序：
   - `Utility.tjs`
   - `AffineLayer.tjs`
   - `Sprite.tjs`
   - `Window.tjs`
   - `MessageArea.tjs`
   - `KAGParserCompat.tjs`
   - `ScController.tjs`
   - `SelectItem.tjs`
   - `GameSceneManager.tjs`
   - `ADVObject.tjs`
   - `ADVScreen.tjs`
   - `AnimationSequence.tjs`
   - 游戏固有脚本
5. 初始化随机数、系统注册表、主窗口、声音层、保存用资源。

`k2compat.tjs` 会 hook `Plugins.link`。其中 `extrans`、`menu`、`layerExDraw`、`fstat`、`KAGParser*` 会被特殊处理或跳过。也就是说，`Initialize.tjs` 看起来仍然调用 `KAGParserEx.dll`，但当前兼容策略实际上依赖 `KAGParserCompat.tjs`。

## begin.tjs

实际被日志命中的 `content-data/begin.tjs`：

1. 前台显示主窗口。
2. 创建 `global.game = new SceneManager(win)`。
3. 如果存在 `-newgame` 参数：
   - `game.setScene(SCENE_ADV)`
   - 获取 ADV 场景
   - `adv.start("00_z000", "")`
4. 否则进入标题场景。

## 需要先确认的问题

下一位接手者应先确认 krkrZ 当前项目目录：

- 如果项目目录是根目录，应执行根目录 `startup.tjs`。
- 如果项目目录是 `content-data/`，日志会继续匹配 `content-data/startup.tjs`。

在未确认入口前，不建议直接修启动链，因为同名脚本有多个副本。
