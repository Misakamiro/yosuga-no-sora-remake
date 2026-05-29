# 当前 bug 与日志证据

## 日志来源

检查文件：

```text
savedata/krkr.console.log
```

最后记录时间为 2026-05-25 02:58:06。该日志来自 `F:\Galgame\yosuga_krkrZ\savedata\krkr.console.log`，当前工作目录是 `C:\Users\misakamiro\Downloads\Compressed\yosuga_krkrZ\yosuga_krkrZ`。因此日志可用于判断历史运行错误，但启动路径和文件副本仍要重新确认。

## 已记录错误

### 1. KAGParser 缺失

日志记录：

```text
An exception occured at animationsequence.tjs(2)
class AnimationSequenceController extends KAGParser
!!! INFORM: 未找到 Member "KAGParser"
```

当前源码仍存在：

```text
system/AnimationSequence.tjs: class AnimationSequenceController extends KAGParser
```

原因判断：

- `k2compat.tjs` 对 `KAGParser*` 插件执行跳过逻辑。
- `system/KAGParserCompat.tjs` 定义的是 `class KAGParserCompat`。
- `system/ScController.tjs` 已改为 `extends KAGParserCompat`。
- `system/AnimationSequence.tjs` 仍依赖全局 `KAGParser` 名称。

最小修复方向：给 `KAGParser` 提供兼容别名/薄封装，或只把 `AnimationSequenceController` 改成继承 `KAGParserCompat`。优先建议兼容层修复，因为这是 krkr2 API 缺失。

### 2. MessageFrame 旧版点击路径缺 `_parent`

日志记录：

```text
An exception occured at messageframe.tjs(1414/1418) onButtonDownL
!!! INFORM: 未找到 Member "_parent"
```

当前 `system/MessageFrame.tjs` 的 `JumpMenu.onButtonDownL` 已是直接调用：

```text
onMenuDecide(target);
```

因此该错误更像历史中间版本残留，当前文件不一定还能复现。后续需要以新启动日志确认。

### 3. MessageFrame 旧版点击路径缺 delegate

日志记录：

```text
An exception occured at messageframe.tjs(557) onMenuDecide
!!! INFORM: 未找到 Member "delegate"
```

当前 `system/MessageFrame.tjs` 第 550 行附近的 `onMenuDecide` 不再调用 `delegate`，而是直接按事件名处理。这也更像历史中间版本残留。

### 4. 跳转下一选项缺 setTimeout / invokeAfterEvent

日志记录过两种尝试：

```text
window.setTimeout(function(){ _advObj.jump(ADVScreen.JUMPSTATE_SELECT_NEXT); }, 1);
!!! INFORM: 未找到 Member "setTimeout"
```

以及：

```text
System.invokeAfterEvent(function(){ _advObj.jump(ADVScreen.JUMPSTATE_SELECT_NEXT); }, 1);
!!! INFORM: 未找到 Member "invokeAfterEvent"
```

当前 `system/MessageFrame.tjs` 已不再使用这两个 API：

```text
case "jumpSelectPrev" : _advObj.jump(ADVScreen.JUMPSTATE_SELECT_PREV); break;
case "jumpSelectNext" : _advObj.jump(ADVScreen.JUMPSTATE_SELECT_NEXT); break;
```

当前结论：

- 历史失败确实是 krkrZ 缺少浏览器式/非标准延迟 API 导致。
- 当前源码已经绕过这两个缺失 API。
- 但最新日志被语法错误挡住，没有证据证明“跳转到下个选项”已经通过完整运行验证。

### 5. 最新日志为语法错误

最后一次日志：

```text
02:58:06 语法有误 （syntax error） at line 6322
02:58:06 !!! INFORM: 语法有误 （syntax error）
```

该错误发生在：

```text
startup.tjs -> Status.tjs -> Initialize.tjs
```

日志没有打印 `startup.tjs: Initialize.tjs loaded`，说明错误发生在 `Initialize.tjs` 加载系统脚本过程中。结合 `Initialize.tjs` 的脚本加载顺序，优先怀疑范围是 `ADVScreen.tjs`、`AnimationSequence.tjs` 或之后的系统脚本。

注意：当前文件有多个副本：

- `system/ADVScreen.tjs`
- `content-data/system/ADVScreen.tjs`
- `content-data/system/system/ADVScreen.tjs`
- `content-data/system_modified/ADVScreen.tjs`

下一步必须先确认实际加载的是哪一份，再定位 line 6322。

## k2compat 当前已覆盖内容

`k2compat.tjs` 当前覆盖/处理了：

- `Plugins.link` hook。
- 跳过或特殊处理：
  - `extrans`
  - `menu`
  - `layerExDraw`
  - `fstat`
  - `KAGParser*`
  - `windowEx`
- `System.inputString` 延迟加载。
- `Pad` 延迟加载。
- `MenuItem` 空对象兜底。
- `Window.innerSunken`、`Window.showScrollBars` dummy property。
- `Window.PassThroughDrawDevice` dummy object。
- `Debug.console`、`Debug.controller`、`Debug.scripted`、`Debug.watchexp` dummy object。
- `Storages.copyFile`、`Storages.deleteFile` stub。
- `k2compat/*` 内的 Win32 dialog、font select、modeless、pad、screen/desktop info 等兼容脚本。

## 当前明显缺口

- 没有全局 `KAGParser` 兼容名，导致仍继承 `KAGParser` 的脚本会失败。
- `Storages.copyFile`、`Storages.deleteFile` 只是空 stub，会吞掉真实保存/复制/删除需求。
- `Storages.readAllText` 没有在 `k2compat.tjs` 中补；`KAGParserCompat.tjs` 依赖它，缺失时只能依靠 `.ctx`。
- 没有 `System.invokeAfterEvent`、`window.setTimeout` 兼容实现，但当前选项跳转路径已经不再调用它们。
- `KAGParserCompat.tjs` 是轻量解析器，尚未完整覆盖 KAGParserEx 的条件、宏、标签参数、行号、特殊标签行为。

## 跳转到下个选项的当前判断

当前源码路径：

```text
MessageFrame.onMenuDecide("jumpSelectNext")
  -> _advObj.jump(ADVScreen.JUMPSTATE_SELECT_NEXT)
  -> ADVScreen.jumpNextStart()
  -> ADVScreen.jumpStart(jumpNextWork)
  -> ADVScreen.jumpNextWork()
  -> ADVScreen.jumpScProcess(...)
```

和 krkrZ 兼容相关的风险：

- 若 `KAGParserCompat.getNextTag()` 与原 KAGParserEx 行为不同，`jumpScProcess` 找下一次 `startselect` 的过程可能不准。
- 若 `goToLabel()` 没有按原引擎抛错/定位，选择分支回放会静默失败。
- 若 `assignStruct`、数组/字典复制语义在 krkrZ 下和 krkr2 不一致，`_stackSelect`、`_logSelect`、`_jumpParam` 可能错位。
- 目前最新日志没有跑到可验证跳转的阶段，因此不能判定功能已恢复。
