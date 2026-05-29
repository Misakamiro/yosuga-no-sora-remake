# 标签支持映射

日期：2026-05-29

本表只覆盖本轮指定标签和普通正文链路。结论基于 `system/KAGParserCompat.tjs`、`system/ScController.tjs`、`system/ADVScreen.tjs`、`system/MessageFrame.tjs` 以及 `scenario/*.ks` 抽样/搜索。

## 1. 总览

| 剧本写法 | 解析器当前 tagname | Handler 键 | 当前状态 | 影响 |
|---|---:|---:|---|---|
| 普通正文 | 无 tag | `ch` | 不支持 | 正文不会进入消息缓冲 |
| `@Talk` | `Talk` | `talk` | 大小写不匹配 | 不会设置说话人、语音、换行捕获状态 |
| `@Hitret` | `Hitret` | `hitret` | 大小写不匹配 | 不会输出消息、等待点击、写已读和历史 |
| `@AddSelect` | `AddSelect` | `addselect` | 大小写不匹配 | 不会累积选项 |
| `@StartSelect` | `StartSelect` | `startselect` | 大小写不匹配 | 不会显示选项或等待选择 |
| `@SetSelect` | `SetSelect` | `setselect` | 理论大小写不匹配；当前未在 `.ks` 搜到 | 自动设置选择结果不可用 |
| `@SelectTerminate` | `SelectTerminate` | `selectterminate` | 大小写不匹配 | 下一选项跳转终止标记不可用 |
| `@Change` | `Change` | `change` | 大小写不匹配 | 剧本文件切换不可用 |

## 2. 标签出现情况

在 `scenario` 和 `content-data/scenario` 的 `.ks` 中搜索到：

| 标签 | 大写写法数量 | 小写写法数量 |
|---|---:|---:|
| `@Talk` | 78,688 | 0 |
| `@Hitret` | 78,688 | 0 |
| `@AddSelect` | 16 | 0 |
| `@StartSelect` | 8 | 0 |
| `@Change` | 608 | 0 |

说明：`scenario` 和 `content-data/scenario` 内容重复，所以数量包含两套目录。即便只看任一目录，写法也是首字母大写。

## 3. 普通正文

示例：

```ks
@Talk name=悠
穹，起床啦！早饭做好了哦！
@Hitret id=671
```

期望：

- `@Talk` 设置 `_name/_voice/_pan/_volume`。
- 正文进入 `ch(elm.text)`。
- `@Hitret` 调用 `hitret(id)`，再输出消息。

当前：

- `_parseKagScript()` 遇到普通字符只执行 `pos++`。
- 不会生成 `%[tagname:"ch", text:"穹，起床啦！早饭做好了哦！"]`。
- 因此即使修复 `Talk/Hitret` 大小写，正文仍然为空。

## 4. @Talk

Handler：`ADVScreen.getHandlers().talk`

作用：

- 默认 name 为 `心の声`。
- 读取 `name`、`voice`、`pan`、`vol`、`freeformat`。
- 调用 `talk(name, voice, pan, vol, freeFormat)`。
- 把 `_scCtrl.ignoreCR` 设为 `false`，注释说明范围是 `@Talk` 到 `@Hitret`。

当前风险：

- 剧本写 `@Talk`，handler 键是 `talk`。
- `ignoreCR` 标记被设置了，但兼容解析器没有用它处理正文或换行。

## 5. @Hitret

Handler：`ADVScreen.getHandlers().hitret`

作用：

- 把 `_scCtrl.ignoreCR` 设为 `true`。
- 读取 `id`，调用 `hitret(id, newline)`。
- 非跳过状态下等待 `click` 或 `timeout`。

`ADVScreen.hitret()` 关键行为：

- 处理 `_mess` 的修饰、ruby、强制换行。
- 通过 `ChkReadFlag(id)` 判断已读。
- 未读时调用 `SetReadFlag(id, true)`。
- 调用 `addLog()` 写历史。
- 非加载/非跳转时调用 `outputMessage()`。

当前风险：

- `@Hitret` 不会匹配 `hitret`，所以已读、历史、等待、输出都会断。
- `jumpScProcess()` 里也按小写 `"hitret"` 判断，所以跳转重放同样会断。

## 6. @AddSelect

Handler：`ADVScreen.getHandlers().addselect`

作用：

- 调用 `addSelect(elm)`。
- `addSelect()` 把整个 elm 复制进 `_select`。

当前风险：

- 剧本写 `@AddSelect`，不会匹配 `addselect`。
- 选项不会进入 `_select`，即使后续 `StartSelect` 被调用也没有可显示项。

## 7. @StartSelect

Handler：`ADVScreen.getHandlers().startselect`

作用：

- 正常模式调用 `startSelect(elm)`。
- `startSelect()` 设置 `SELECTTERMINATE_FLAG`、自动保存、`_hitretNum++`、绘制选项按钮。
- 等待 `_scCtrl.trigger("select")`。

选择后：

- `onButtonDownL()` 找到按钮索引。
- 调用 `decideSelect(i)`。
- `decideSelect()` 写 `_ret_select`、`_logSelect`、历史日志、`_stackSelect`，再按选项 label 跳转。

当前风险：

- 剧本写 `@StartSelect`，不会匹配 `startselect`。
- 选择 UI 不会出现。
- 跳转栈 `_stackSelect` 不会记录该选项。

## 8. @SetSelect

Handler：`ADVScreen.getHandlers().setselect`

作用：

- `_ret_select = int GetElm(elm.id, 0)`。

当前搜索结果：

- 未在 `.ks` 中搜到 `SetSelect/setselect`。
- `jumpScProcess()` 支持重放 `setselect`，但仍要求 tagname 小写。

风险：

- 如果后续剧本或宏使用 `@SetSelect`，当前大小写问题会导致自动选择状态不同步。

## 9. @SelectTerminate

Handler：`ADVScreen.getHandlers().selectterminate`

作用：

- `SetParam(SELECTTERMINATE_FLAG, true)`。
- `jump()` 中的“下一选项”会检查 `ChkFlagOn(SELECTTERMINATE_FLAG)`，已终止则不允许跳。

当前出现点：

- `scenario/00_z014.ks:526`
- `content-data/scenario/00_z014.ks:526`

当前风险：

- 大小写不匹配导致终止标记不会被设置。
- 如果下一选项跳转可用后，这会造成结尾后仍显示“跳转下一选项”入口。

## 10. @Change

Handler：`ADVScreen.getHandlers().change`

作用：

- `elm.target.split(",/")`
- 调用 `change(param[0], param[1], true)`
- `fScChange=true` 时，把当前剧本 EOF 的 `_logSelect` 保存到 `_logSelectEOF[_scenario]`。
- 加载目标剧本。

当前风险：

- 剧本写 `@Change`，不会匹配 `change`。
- 文件链路会断，`_logSelectEOF` 不会保存，历史预览和跨文件跳转恢复会受影响。

## 11. 下一步建议

优先级建议：

1. 在解析阶段统一 tagname 小写，`*label` 例外。
2. 在解析阶段把非空普通正文行转换为 `ch text=...`。
3. 对 `@Talk + 正文 + @Hitret` 做离线 token 回归。
4. 对 `@AddSelect + @StartSelect + @Change` 做最小选项回归。
5. 再补 `@if/@endif`，否则 `00_z005` 的选项前条件会继续影响真实路线。

## 12. 交接检查项

复现步骤：

- 对 `scenario/00_z005.ks` 开头片段检查 `@Talk + 正文 + @Hitret`。
- 对 `scenario/00_z005.ks:503-505` 检查 `@AddSelect + @StartSelect`。
- 对 `scenario/00_z014.ks:526` 检查 `@SelectTerminate`。
- 对任意文件尾部 `@Change target=...` 检查跨文件切换。

当前实际表现：

- 指定标签在剧本中均为首字母大写，当前 handler 键为小写。
- 普通正文没有 token。

期望表现：

- 每个指定标签都能进入对应 handler。
- 正文进入 `ch` 或等价消息追加流程。

涉及文件：

- `system/KAGParserCompat.tjs`
- `system/ADVScreen.tjs`
- `system/ScController.tjs`
- `system/MessageFrame.tjs`
- `scenario/*.ks`
- `content-data/scenario/*.ks`

