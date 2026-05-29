# 最小修复计划

## 修复原则

1. 先确认实际启动入口和实际加载副本。
2. 每次只修一个根因。
3. 优先补 krkr2 API 兼容层，不先重写大文件。
4. 修复后用启动日志和实际操作验证，不只看语法。

## Phase 1：确认真实入口

目标：确认 krkrZ 实际执行的 `startup.tjs` 是根目录版本还是 `content-data/startup.tjs`。

建议方法：

1. 备份当前 `savedata/krkr.console.log`。
2. 启动一次 krkrZ。
3. 检查新日志里出现的是：
   - `Auto-paths: system/ and content-data/ registered`
   - 还是 `Auto-paths: system/ registered`
4. 如果仍是后者，说明实际入口是 `content-data/startup.tjs` 或旧副本。

验收标准：

- 文档中记录实际入口。
- 后续修复只改实际加载链路上的文件。

## Phase 2：修 KAGParser 名称缺失

目标：解决 `AnimationSequenceController extends KAGParser` 这类 krkr2 API 名称缺失。

最小方案 A，优先推荐：

- 在 `KAGParserCompat.tjs` 加一个全局兼容类/别名，让 `KAGParser` 指向兼容实现。
- 理由：这是引擎兼容层职责，能覆盖所有仍引用 `KAGParser` 的脚本。

最小方案 B：

- 只把 `system/AnimationSequence.tjs` 改成 `extends KAGParserCompat`。
- 理由：改动小，但如果后续还有脚本依赖 `KAGParser`，会继续暴露同类问题。

验收标准：

- 新日志不再出现 `未找到 Member "KAGParser"`。
- `Initialize.tjs` 能加载到 `startup.tjs: Initialize.tjs loaded`。

## Phase 3：定位最新 syntax error

目标：解决最新日志的 `syntax error at line 6322`。

建议方法：

1. 确认实际加载的 `ADVScreen.tjs` 副本。
2. 用 krkrZ 新日志确认报错是否仍是 line 6322。
3. 对照实际副本的 line 6322 附近，检查：
   - 非法的函数默认参数。
   - 不被 krkrZ 接受的省略参数写法。
   - 半成品调试代码。
   - 编码/控制字符导致的行号错位。
4. 如果 line 6322 不能直接定位，按 `Initialize.tjs` 的 `LoadScript` 顺序临时增加更细日志，确定具体脚本名。

验收标准：

- 新日志不再出现启动期 syntax error。
- 启动能走到 `begin.tjs`。

## Phase 4：验证跳转到下个选项

目标：确认“跳转到下个选项”是否仍失败。

建议验证路径：

1. 用 `-newgame=yes` 进入 `00_z000`。
2. 到第一个可选项附近，例如：
   - `scenario/00_z005.ks` 的 `@AddSelect` / `@StartSelect`
   - 或 `scenario/00_z008.ks`
   - 或 `scenario/00_z012.ks`
3. 在一次选择后，等到普通 `Hitret` 状态。
4. 点击系统菜单里的下一选项按钮，或按右方向键。
5. 检查：
   - 是否弹出“要跳转下一选项吗？”
   - 确认后是否自动前进到下一个 `@StartSelect`
   - 日志是否出现 `setTimeout`、`invokeAfterEvent`、`delegate`、`KAGParser`、`syntax error`

验收标准：

- 能从一次已选择分支跳到下一次选项。
- `_stackSelect` 和 `_logSelect` 没有导致前后选项错乱。
- 如果失败，日志里必须记录新的具体异常。

## Phase 5：补齐必要 API，不扩大范围

只有在日志明确证明缺 API 时才补：

- 如果缺 `KAGParser`：补兼容名。
- 如果缺 `Storages.readAllText`：补真实读取实现或确认 `.ctx` 生成链路，不要空 stub。
- 如果缺 `System.invokeAfterEvent`：优先保持当前直接调用跳转路径；只有其他系统脚本仍依赖时再用 `AsyncTrigger`/`Timer` 做兼容。
- 如果 `copyFile/deleteFile` 影响存档：不要继续空 stub，应改成真实文件操作或明确禁用依赖路径。

## 不建议一开始做的事

- 不要重写 `ADVScreen.tjs`。
- 不要大改 UI 或资源。
- 不要同时改多个 `content-data/system*` 副本。
- 不要把日志里的旧错误当成当前必现错误，必须用新启动日志确认。
