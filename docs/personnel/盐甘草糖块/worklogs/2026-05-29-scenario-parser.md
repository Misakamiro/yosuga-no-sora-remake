# 2026-05-29 剧本解析模块工作记录

## 1. 本次任务

负责模块：

- 剧本解析
- 选项跳转
- 已读状态
- 历史回放

本轮按要求只检查和写文档，没有直接重写解析器。

## 2. 检查过的文件

系统文件：

- `system/KAGParserCompat.tjs`
- `system/ScController.tjs`
- `system/ADVScreen.tjs`
- `system/MessageFrame.tjs`

剧本文件：

- `scenario/*.ks`
- `content-data/scenario/*.ks`
- `content-data/scenario/*.ctx`

重点抽样：

- `scenario/00_z005.ks`
- `scenario/00_a004.ks`
- `scenario/00_z014.ks`
- `content-data/scenario/00_a004.ks`
- `content-data/scenario/00_a004.ks.ctx`

## 3. 做了什么

1. 确认目标项目目录：
   - `C:\Users\misakamiro\Downloads\Compressed\yosuga_krkrZ\yosuga_krkrZ`

2. 确认该目录不是 Git 仓库：
   - `git status --short` 返回 `fatal: not a git repository`。
   - 因此本轮不能用 git diff 作为变更证明，改用文件检查和内容搜索。

3. 阅读 `KAGParserCompat.tjs`：
   - 确认 `.ctx` 优先加载。
   - 确认 `.ctx` 通过 `global.__kag_src__` 提供原始剧本文本。
   - 确认 fallback 是 `Storages.readAllText(storage)`。
   - 确认解析器只识别注释、label、`@tag`、`[tag]`。

4. 阅读 `ScController.tjs`：
   - 确认 `scenarioLoop()` 调用 `loadScenario()` 后再 `start()`。
   - 确认 `onTag()` 使用 `tagHandle[tagname]` 直接查 handler。

5. 阅读 `ADVScreen.tjs`：
   - 确认 handler 键是小写：`talk`、`hitret`、`addselect`、`startselect`、`setselect`、`selectterminate`、`change`。
   - 确认正文显示链路是 `ch -> hitret -> outputMessage -> MessageFrame.output`。
   - 确认已读状态在 `hitret()` 中通过 `ChkReadFlag()` / `SetReadFlag()` 处理。
   - 确认历史通过 `addLog()` 写入。
   - 确认选项选择通过 `decideSelect()` 写 `_logSelect` 和 `_stackSelect`。
   - 确认下一选项跳转通过 `jumpNextWork()` 调 `jumpScProcess(handle, 9999999)`。

6. 阅读 `MessageFrame.tjs`：
   - 确认跳转菜单会调用 `_advObj.jump(ADVScreen.JUMPSTATE_SELECT_NEXT)`。
   - 确认 `MessageFrame.output()` 根据 `fRead` 切换已读文字颜色。

7. 搜索剧本标签：
   - `@Talk`：78,688 次，`@talk`：0 次。
   - `@Hitret`：78,688 次，`@hitret`：0 次。
   - `@AddSelect`：16 次，`@addselect`：0 次。
   - `@StartSelect`：8 次，`@startselect`：0 次。
   - `@Change`：608 次，`@change`：0 次。

8. 做了一个离线 token 复现：
   - 使用 `scenario/00_z005.ks` 开头内容。
   - 按当前解析器逻辑得到 `PlaySe/Cg/Update/Wait/BlackOut/Cg/Talk/Hitret/...`。
   - 普通正文没有 token。

## 4. 当前实际表现

基于当前代码，实际表现是：

- 标签大小写没有归一化，剧本首字母大写标签匹配不到小写 handler。
- 普通正文不会进入 `ch()`，所以消息内容不会进入 `_mess`。
- `@Hitret` 不会触发等待、已读标记、历史记录和消息输出。
- `@AddSelect/@StartSelect` 不会建立选项 UI。
- `@Change` 不会切换剧本文件。
- 下一选项跳转在解析层和扫描层都会遇到大小写断点。

## 5. 期望表现

期望表现：

- `@Talk + 正文 + @Hitret` 能显示一条完整消息。
- `@Hitret id` 能设置已读、写历史并等待点击。
- `@AddSelect` 累积选项，`@StartSelect` 展示并等待选择。
- 选择后写 `_logSelect`、`_stackSelect` 和历史日志。
- “跳转下一选项”能从当前等待状态扫描到下一个 `startselect`。
- `CONFIG.readSkip == 1` 时，遇到未读 `hitret` 应停止。
- `@Change target=xxx` 能切换剧本并保存跨文件选择历史。

## 6. 最小复现步骤

静态复现：

1. 打开 `scenario/00_z005.ks`。
2. 取开头片段：

```ks
@Talk name=悠
穹，起床啦！早饭做好了哦！
@Hitret id=671
```

3. 按 `KAGParserCompat._parseKagScript()` 解析。
4. 实际得到 `Talk` 和 `Hitret`，没有正文 token。
5. 交给 `ScController.onTag()` 后查不到小写 `talk/hitret` handler。

选项复现：

1. 打开 `scenario/00_z005.ks:503-505`。
2. 片段为：

```ks
@AddSelect text=敲门 hint=穹
@AddSelect text=今天就先这样吧 hint=奈緒/瑛/一葉/初佳
@StartSelect
```

3. 当前解析得到 `AddSelect/StartSelect`。
4. Handler 需要 `addselect/startselect`。
5. 实际选项不会进入 `_select`，选择 UI 不会正常建立。

## 7. 涉及文件

本轮新增文档：

- `docs/modules/scenario-parser/README.md`
- `docs/modules/scenario-parser/tag-support-map.md`
- `docs/modules/scenario-parser/select-jump-analysis.md`
- `docs/modules/scenario-parser/regression-test-plan.md`
- `docs/worklogs/2026-05-29-scenario-parser.md`

本轮只读参考：

- `system/KAGParserCompat.tjs`
- `system/ScController.tjs`
- `system/ADVScreen.tjs`
- `system/MessageFrame.tjs`
- `scenario/*.ks`
- `content-data/scenario/*.ks`
- `content-data/scenario/*.ctx`

## 8. 风险说明

不建议直接重写整个解析器。

原因：

- 剧本文本量大，`Talk/Hitret` 成对数量很高。
- 选项跳转依赖 `_hitretNum`、`_logSelect`、`_stackSelect`、`_logSaveInfo`，改错会影响保存、读取、历史和跳转。
- `.ctx` 当前只是原始文本包装，不是可靠 token 缓存。
- 条件分支 `@if/@endif` 还没有确认支持，修复基础解析后可能出现下一层路线问题。

## 9. 下一步建议

建议下一轮按这个顺序推进：

1. 最小修复 `KAGParserCompat`：
   - tagname 小写化。
   - 普通正文转换成 `ch text=...`。
   - label 保持 `*label`。

2. 写离线解析测试：
   - `Talk -> ch -> Hitret`
   - `AddSelect -> StartSelect`
   - `SelectTerminate`
   - `Change`

3. 做游戏内手动验证：
   - `00_z005` 第一句显示。
   - `00_z005` 第一个选项显示和选择。
   - 下一选项跳转。
   - 历史回放跳转。

4. 再评估 `@if/@endif`：
   - `00_z005.ks:502` 选项前已经出现条件。
   - 如果不补条件分支，选项路线可能仍不符合原作逻辑。

