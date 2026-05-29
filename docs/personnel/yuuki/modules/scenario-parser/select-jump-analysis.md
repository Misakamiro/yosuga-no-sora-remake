# 选项跳转分析

日期：2026-05-29

本文件重点分析“跳转到下个选项”的当前调用链、断点、选择历史、已读状态和跳转栈关系。

## 1. 用户入口

入口有两处：

- 消息框系统菜单：`MessageFrame.onMenuDecide("jumpSelectNext")`
- 键盘快捷键：`ADVScreen.onKeyDown()` 中的下一选项快捷键

菜单链路：

```text
JumpMenu.onButtonDownL()
-> MessageFrame.onMenuDecide("jumpSelectNext")
-> ADVScreen.jump(ADVScreen.JUMPSTATE_SELECT_NEXT)
```

菜单按钮可用性由 `SystemMenu.onButtonEnter()` 刷新：

```text
jumpPrevValid(_adv.countOfStackSelect() != 0)
jumpNextValid(!_adv.isSelectTerminate() && !_adv.isSelect())
```

也就是说，“下一选项”按钮是否可点，依赖：

- 当前没有 `SELECTTERMINATE_FLAG`。
- 当前不在选择 UI 中。

## 2. jump() 前置条件

`ADVScreen.jump(type)` 对下一选项有这些拦截：

1. `if(!_hitretState) return;`
2. `if(IsRecollect()) return;`
3. `if(ChkFlagOn(SELECTTERMINATE_FLAG)) return;`
4. `if(isSelect()) return;`
5. 弹确认窗口，确认后调用 `jumpNextStart()`。

当前实际断点：

- 因 `@Hitret` 大小写不匹配，`hitret` handler 不会执行，`_hitretState` 很可能不会进入正常等待状态。
- 因 `@StartSelect` 大小写不匹配，`isSelect()` 和选择状态也不能代表真实剧本状态。
- 因 `@SelectTerminate` 大小写不匹配，终止标记不会正确设置。

## 3. 下一选项跳转调用链

确认后：

```text
jumpNextStart()
-> _jumpState = JUMPSTATE_SELECT_NEXT
-> jumpStart(jumpNextWork)
-> BeginLoad(jumpNextWork)
-> jumpNextWork()
-> jumpScProcess(handle, 9999999)
-> 遇到 startselect 后停止
-> handle["startselect"](elm)
-> jumpEnd()
```

`jumpStart()` 会：

- 停止当前脚本等待。
- 清掉 `_hitretState`。
- 停止 voice/se。
- 如果当前正在选择，调用 `endSelect()`。
- 停止 skip/auto。
- 隐藏消息框。
- 进入 loading 流程。

下一选项跳转不会重新 `change()`，它从当前 `_scCtrl.getNextTag()` 继续往后扫描。

## 4. jumpScProcess() 如何找下个选项

`jumpScProcess(handle, hitretNum, logSelect, tagList, fGetTag)` 是跳转重放核心。

下一选项传入 `hitretNum=9999999`，含义是向后扫描很远，直到：

- 遇到未读 `hitret` 且配置为仅已读跳过。
- 或遇到 `startselect`。
- 或遇到剧本终端/切换。

关键逻辑：

- 遇到 `hitret/hitwait`：
  - `hitretNum--`
  - 获取 `id`
  - `fRead = ChkReadFlag(id)`
  - 如果是下一选项跳转、未读且 `CONFIG.readSkip == 1`，则 `fLoop = false`
  - 调用 `handle[elm.tagname](elm)` 重放消息状态

- 遇到 `startselect`：
  - `_hitretNum++`
  - `hitretNum--`
  - 如果仍需继续，会按 `logSelect` 自动 `decideSelect()`
  - 如果 `_jumpState == JUMPSTATE_SELECT_NEXT`，则 `fLoop = false`
  - 返回该 `startselect` 给 `jumpNextWork()`

## 5. 当前在哪里断

主要断点按优先级：

1. 解析器不把 tagname 归一化为小写。
   - 剧本实际是 `@Hitret/@StartSelect/@AddSelect/@Change`。
   - `jumpScProcess()` 判断的是 `"hitret"`、`"startselect"`、`"addselect"`、`"change"`。
   - 因此跳转扫描识别不到目标标签。

2. 解析器不生成正文 `ch`。
   - 跳转过程中即使遇到 `hitret`，也没有正文积累到 `_mess`。
   - 历史回放恢复最后一句时会缺正文。

3. `@AddSelect` 不会进入 `_select`。
   - `startSelect()` 依赖 `_select.count > 0`。
   - 没有选项数据时，`startSelect()` 会返回 false。

4. `@Change` 不会执行。
   - 下一选项跳转如果跨文件，当前文件尾的 `@Change` 无法切换到目标文件。
   - `_logSelectEOF` 也不会保存当前文件末尾选择历史。

5. `@if/@endif` 没有 handler。
   - `00_z005.ks:502` 的选项前有 `@if exp="ChkFlagOn(151)"`。
   - 修复大小写和正文后，条件分支仍可能不按原 KAG 语义执行。

## 6. 是否和选择历史有关

有关，但不是第一断点。

选择历史相关字段：

- `_logSelect`：当前剧本路径内已经做过的选择。
- `_stackSelect`：用于“前一个选项”跳回的栈。
- `_logSelectEOF`：跨剧本文件末尾保存的选择历史。
- `_logSaveInfo`：每个剧本起点的保存快照，用于普通跳转和历史回放恢复。

选择发生时：

```text
decideSelect(id)
-> _ret_select = id + 1
-> _logSelect.add(_ret_select)
-> addLog("■选 项■", ...)
-> _stackSelect.push(...)
-> 如果选项有 label，则 goToLabel()
-> trigger("select")
```

下一选项跳转本身不依赖 `_stackSelect` 才能向前扫；它依赖当前 `_scCtrl` 的解析位置和 `startselect` 标签。`_stackSelect` 主要服务“前一个选项”和保存/读取。

但如果 `AddSelect/StartSelect` 没跑，`decideSelect()` 不会发生，选择历史和跳转栈都会缺失，后续历史回放、前一选项和跨文件恢复会连带失真。

## 7. 是否和已读状态有关

有关。

下一选项跳转在 `jumpScProcess()` 里会检查：

```text
_jumpState == JUMPSTATE_SELECT_NEXT
!fRead
CONFIG.readSkip == 1
```

如果配置是“只跳过已读”，遇到未读 `Hitret id` 应停止，不应继续跳到选项。

当前风险：

- `Hitret` 大小写不匹配，已读判断不会在跳转扫描中触发。
- 正常阅读时 `SetReadFlag(id, true)` 也不会触发。
- 修复大小写后，未读停止逻辑会开始生效，需要测试 `CONFIG.readSkip` 两种配置。

## 8. 是否和历史回放有关

有关。

历史窗口会调用：

```text
SystemWindow -> _adv.jump(ADVScreen.JUMPSTATE_NORMAL, %[scenario, hitret, select])
```

普通历史跳转链路：

```text
jumpNormalWork()
-> 用 _logSaveInfo[scenario] 恢复剧本起点状态
-> eraseLogAfter(startLogCount)
-> 裁剪 _stackSelect
-> change(scenario)
-> jumpScProcess(handle, hitretNum, logSelect)
-> 输出最后一条历史消息
```

如果解析层没有正文、`hitret`、`change`，历史回放会同时出现：

- 无法正确重放到目标 `hitret`。
- 无法恢复最后一句文本。
- 跨文件历史跳转缺少 `_logSelectEOF`。

## 9. 最小复现流程

静态流程：

1. 从 `scenario/00_z005.ks` 进入。
2. 阅读到 `scenario/00_z005.ks:503-505`：

```ks
@AddSelect text=敲门 hint=穹
@AddSelect text=今天就先这样吧 hint=奈緒/瑛/一葉/初佳
@StartSelect
```

3. 当前解析器产出的 tagname 是 `AddSelect`、`StartSelect`。
4. `ScController.onTag()` 查找 handler 时需要 `addselect`、`startselect`。
5. 实际结果：选项不进入 `_select`，`startSelect()` 不会被调用。

运行流程：

1. 进入 `00_z005`。
2. 如果第一句已无法显示，先记录 `Talk/Hitret` 断点。
3. 若临时修复大小写和正文后，再推进到 `00_z005.ks:503`。
4. 选择任意选项。
5. 点击“跳转下一选项”。
6. 观察是否停在下一个 `@StartSelect`，或因未读 `Hitret` 停止。

当前实际表现：

- 原始代码状态下，选项跳转链路大概率在进入 `jump()` 前就不成立，因为 `Hitret` 没有建立正常等待状态。
- 即使强行调用 `jumpNextWork()`，`jumpScProcess()` 也无法识别大写 `StartSelect`。

期望表现：

- 已读模式下，从当前消息跳到下一个选项。
- 仅已读跳过时，遇到未读 `Hitret id` 停止。
- 到达选项时展示选项 UI。
- 选择后 `_logSelect` 和 `_stackSelect` 正确记录。

## 10. 下一步建议

短期修复顺序：

1. 先修解析器 tagname 小写和正文 `ch` token。
2. 给 `jumpScProcess()` 建离线 token 输入测试，覆盖 `Hitret -> StartSelect`。
3. 手动验证 `00_z005` 第一个选项。
4. 再验证 `00_z014` 的 `SelectTerminate` 对下一选项按钮的影响。
5. 最后补跨文件 `Change` 和历史回放。

## 11. 交接检查项

复现步骤：

- 普通消息等待中点击菜单的“下一选项”。
- 或使用 `scenario/00_z005.ks:503-505` 作为最小选项目标。

当前实际表现：

- 原始状态下 `Hitret` 没有建立可靠等待状态。
- `jumpScProcess()` 识别小写 `startselect`，但剧本解析结果是 `StartSelect`。

期望表现：

- 下一选项跳转能扫描到下一个 `startselect`。
- 仅已读跳过配置下，遇到未读 `hitret` 停止。

涉及文件：

- `system/ADVScreen.tjs`
- `system/MessageFrame.tjs`
- `system/KAGParserCompat.tjs`
- `scenario/00_z005.ks`
- `scenario/00_z014.ks`

