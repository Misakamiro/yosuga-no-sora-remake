# 2026-05-29 engine-compat 工作日志

## 本次任务

负责“缘之空 krkr2 -> krkrZ 引擎兼容与移植 bug 修复”模块，先做只读启动链检查、日志错误整理、兼容层覆盖分析、跳转下一选项风险判断，并创建交接文档。

## 修改了哪些文件

本次只新增文档，没有修改引擎源码：

- `docs/modules/engine-compat/README.md`
- `docs/modules/engine-compat/startup-chain.md`
- `docs/modules/engine-compat/current-bugs.md`
- `docs/modules/engine-compat/fix-plan.md`
- `docs/worklogs/2026-05-29-engine-compat.md`

没有修改：

- `startup.tjs`
- `k2compat.tjs`
- `tvpwin32.cf`
- `system/*.tjs`
- `savedata/krkr.console.log`
- `k2compat/*`
- DLL
- 图片/资源/打包文件

## 每个修改的原因

- `README.md`：作为 engine-compat 模块入口，汇总范围、当前结论、风险和文档索引。
- `startup-chain.md`：记录 `startup.tjs -> k2compat.tjs -> Status.tjs -> Initialize.tjs -> begin.tjs`，并指出日志实际更像 `content-data/startup.tjs`。
- `current-bugs.md`：记录日志里的语法错误、插件/API 错误、`KAGParser` 缺失、跳转下一选项历史错误和当前风险。
- `fix-plan.md`：给下一位接手者最小修复顺序，避免直接重写大文件。
- `2026-05-29-engine-compat.md`：本次交接记录，说明改动、原因、验证状态、未解决问题和下一步。

## 只读检查结果

目标目录存在：

```text
C:\Users\misakamiro\Downloads\Compressed\yosuga_krkrZ\yosuga_krkrZ
```

该目录不是 git 仓库，无法用 `git diff` 做版本追踪。

核心文件存在：

- `startup.tjs`
- `k2compat.tjs`
- `tvpwin32.cf`
- `system/Initialize.tjs`
- `system/KAGParserCompat.tjs`
- `system/ScController.tjs`
- `system/ADVScreen.tjs`
- `system/MessageFrame.tjs`
- `savedata/krkr.console.log`

## 是否通过启动测试

本次没有启动 krkrZ 做新的运行测试。

原因：

- 用户要求先做只读检查当前启动链。
- 启动游戏会刷新 `savedata/krkr.console.log`，可能覆盖现有证据。
- 本次任务重点是交接文档和最小修复计划，没有授权修改源码。

当前已有日志显示：

- 之前有多次能走到 `begin.tjs` 并进入 `ADVScreen`。
- 最新一次日志停在 `语法有误 （syntax error） at line 6322`，没有走到 `Initialize.tjs loaded`。

所以启动测试状态应记录为：未执行新的启动测试；根据现有日志，最新运行未通过启动。

## 是否还有未解决 bug

有，至少包括：

1. 实际启动入口未确认：根目录 `startup.tjs` 和 `content-data/startup.tjs` 内容不同，日志匹配后者。
2. `KAGParser` 全局名缺失：`AnimationSequenceController extends KAGParser` 仍存在。
3. 最新日志语法错误未定位到具体实际加载文件。
4. “跳转到下个选项”当前源码已去掉 `setTimeout/invokeAfterEvent`，但未通过新运行验证。
5. `KAGParserCompat.tjs` 是轻量解析器，和原 `KAGParserEx.dll` 的行为差异仍可能影响选择分支跳转。
6. `Storages.copyFile/deleteFile` 当前只是 stub，后续可能影响存档/文件操作。

## 下一位接手的人应该继续做什么

1. 先备份 `savedata/krkr.console.log`。
2. 启动一次 krkrZ，确认实际执行的是根目录 `startup.tjs` 还是 `content-data/startup.tjs`。
3. 如果仍报 syntax error，先按 `Initialize.tjs` 加载顺序定位实际脚本名，不要直接改大文件。
4. 最小补 `KAGParser` 兼容名或把 `AnimationSequenceController` 改为继承 `KAGParserCompat`。
5. 启动能进 ADV 后，再验证“跳转到下个选项”：
   - 跑到 `scenario/00_z005.ks`、`00_z008.ks` 或 `00_z012.ks` 的选项点。
   - 选择一次。
   - 等到普通停顿状态。
   - 点击下一选项或按右方向键。
   - 检查是否跳到下一次 `@StartSelect`。
6. 如果失败，用新日志继续按根因修，不要恢复旧的 `setTimeout` 或 `System.invokeAfterEvent` 写法。

## 本次验证

已验证：

- 指定核心文件存在。
- `savedata/krkr.console.log` 可读。
- `k2compat` 文件夹存在并包含 dialog、input、pad、modeless、screen/desktop info 等兼容脚本。
- 当前 `system/MessageFrame.tjs` 不再调用 `delegate`、`window.setTimeout`、`System.invokeAfterEvent`。
- 当前 `system/AnimationSequence.tjs` 仍继承 `KAGParser`。
- 当前日志最新错误为 `syntax error at line 6322`。

未验证：

- 未运行 krkrZ 新启动测试。
- 未验证实际跳转下一选项是否成功。
- 未验证任何修复，因为本次没有改源码。
