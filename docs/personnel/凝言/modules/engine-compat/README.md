# engine-compat 模块说明

## 负责范围

本模块记录“缘之空 krkr2 -> krkrZ 引擎兼容与移植 bug 修复”的启动链、当前错误、兼容层覆盖范围和最小修复计划。

本次只读检查的核心文件：

- `startup.tjs`
- `k2compat.tjs`
- `tvpwin32.cf`
- `system/Initialize.tjs`
- `system/KAGParserCompat.tjs`
- `system/ScController.tjs`
- `system/ADVScreen.tjs`
- `system/MessageFrame.tjs`
- `savedata/krkr.console.log`
- `k2compat/*`
- DLL 加载逻辑

本次没有修改引擎脚本、UI 文件、图片资源、打包文件或 DLL。

## 当前结论

启动链设计目标是：

```text
startup.tjs -> k2compat.tjs -> Status.tjs -> Initialize.tjs -> begin.tjs
```

但日志里的实际入口和根目录 `startup.tjs` 存在差异：日志打印的是 `Auto-paths: system/ registered`，这与 `content-data/startup.tjs` 一致；根目录 `startup.tjs` 当前打印的是 `Auto-paths: system/ and content-data/ registered`。下一位接手者应先确认 krkrZ 当前选中的项目目录和实际执行的 `startup.tjs`，否则会在错误入口上排查。

## 关键风险

- `KAGParserEx.dll` 被 `k2compat.tjs` 的插件 hook 跳过，但当前仍有 `system/AnimationSequence.tjs` 使用 `class AnimationSequenceController extends KAGParser`。日志曾记录 `未找到 Member "KAGParser"`。
- `KAGParserCompat.tjs` 只实现了轻量 KAG 解析器，覆盖 `loadScenario`、`getNextTag`、`goToLabel`、macro 展开等基础路径；它没有完整复刻 krkr2/KAGParserEx 的全部行为。
- “跳转到下个选项”历史失败和缺失 API 有关：日志中出现过 `delegate`、`window.setTimeout`、`System.invokeAfterEvent` 缺失。当前 `system/MessageFrame.tjs` 已改成直接调用 `_advObj.jump(ADVScreen.JUMPSTATE_SELECT_NEXT)`，但最新日志被语法错误挡住，不能证明该功能已通过。

## 文档索引

- [startup-chain.md](startup-chain.md)：启动链和实际入口差异。
- [current-bugs.md](current-bugs.md)：日志错误、缺失 API、选项跳转风险。
- [fix-plan.md](fix-plan.md)：最小修复顺序和验证方法。
- [../../worklogs/2026-05-29-engine-compat.md](../../worklogs/2026-05-29-engine-compat.md)：本次交接工作日志。
