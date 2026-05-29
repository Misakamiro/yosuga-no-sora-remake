# 剧本解析模块交接说明

日期：2026-05-29

模块范围：

- `system/KAGParserCompat.tjs`
- `system/ScController.tjs`
- `system/ADVScreen.tjs`
- `system/MessageFrame.tjs`
- `scenario/*.ks`
- `content-data/scenario/*.ks`
- `content-data/scenario/*.ctx`

本轮只做解析链路分析和交接文档，没有修改运行代码、图像资源、UI 视觉资源或打包文件。

## 1. 当前解析入口

`ADVScreen.start(name, label)` 调用 `change(name, label)`，再调用 `_scCtrl.scenarioLoop(sc, label)`。

`ScController.scenarioLoop(name, label)` 做三件事：

1. 检查 `name + ".ks"` 是否存在。
2. 调用 `loadScenario(name + ".ks")`。
3. 如指定 label，则调用 `goToLabel("*" + label)`。

`ScController` 继承 `KAGParserCompat`，所以实际解析逻辑在 `system/KAGParserCompat.tjs`。

## 2. .ks 和 .ctx 的读取顺序

`KAGParserCompat.loadScenario(storage)` 当前流程：

1. 先确认 `storage` 存在，例如 `00_z005.ks`。
2. 优先检查 `storage + ".ctx"`，例如 `00_z005.ks.ctx`。
3. 如果 `.ctx` 存在，则执行 `Scripts.execStorage(ctxFile)`。
4. 期望 `.ctx` 写入 `global.__kag_src__`，然后把该字符串当成剧本文本继续解析。
5. 如果 `.ctx` 失败或没有写入内容，则尝试 `Storages.readAllText(storage)` 读取 `.ks`。

抽样结果：

- `scenario`、`content-data/scenario`、`content-data/scenario/*.ctx` 各有 306 个文件。
- `.ctx` 当前不是结构化 token 缓存，而是 `global.__kag_src__ = "...原始 ks 文本..."`。
- 因此 `.ctx` 没有补足正文 token、标签大小写或条件分支能力，只是换一种方式提供原始文本。

## 3. 当前解析器实际做了什么

`_parseKagScript(src)` 当前只识别四类内容：

- `;` 开头的注释行：跳过。
- `*label`：加入 tag，形如 `%[tagname:"*label"]`。
- `@Tag key=value`：加入 tag，tagname 保留原始大小写。
- `[tag key=value]`：加入 tag，tagname 保留原始大小写。

它没有做这些事情：

- 没有把普通正文转换成 `ch` 或其他消息标签。
- 没有把 `Talk`、`Hitret`、`AddSelect` 等标签名转成小写。
- 没有处理 KAG 条件块，例如 `@if` / `@endif`。
- 没有处理多行标签。
- 参数拆分只按空格和双引号，未覆盖所有 KAG 写法。

## 4. 普通正文是否能进入消息显示

当前结论：普通正文不能稳定进入消息显示。

原因是普通正文在 `_parseKagScript` 中只会被 `pos++` 跳过，不会生成任何 tag。消息显示链路期望拿到的是 `ch` 标签：

1. `ScController.callback()` 调用 `getNextTag()`。
2. `ScController.onTag(elm)` 按 `elm.tagname` 从 `ADVScreen.getHandlers()` 查 handler。
3. `ch` handler 调用 `ch(elm.text)` 把正文追加进 `_mess`。
4. `hitret` handler 调用 `hitret(id, newline)`。
5. `ADVScreen.hitret()` 处理已读标记、日志和 `outputMessage()`。
6. `outputMessage()` 调用 `MessageFrame.output()`。

但现有剧本是这种格式：

```ks
@Talk name=悠
穹，起床啦！早饭做好了哦！
@Hitret id=671
```

解析器实际产出的前几个 tag 类似：

```text
PlaySe
Cg
Update
Wait
BlackOut
Cg
Talk
Hitret
```

中间的正文行没有对应 tag，`Talk/Hitret` 也保持大写，无法匹配小写 handler。

## 5. 当前实际表现

基于静态链路和离线 token 复现，当前实际表现应为：

- `@Talk` 会进入 `ScController.onTag()`，但 handler 表里只有 `talk`，会被视为未知标签。
- 普通正文行不会进入 `_mess`。
- `@Hitret` 会因为大小写不匹配而不会触发等待、已读标记、历史记录和消息输出。
- `@AddSelect/@StartSelect/@Change` 同样因大小写不匹配无法被 handler 处理。
- 如果实际运行层没有别的外部解析器替换该兼容类，剧本会快速扫过大量未知标签或到达终端，不会正常逐句显示。

## 6. 期望表现

期望链路应满足：

- 标签名进入 handler 前统一为 `ADVScreen.getHandlers()` 使用的小写形式。
- 普通正文在 `@Talk` 和 `@Hitret` 之间转换为 `ch text=正文`，或等价调用 `ch()`。
- `@Hitret id=N` 必须触发 `hitret()`，写入已读状态、历史日志，并等待点击。
- `@AddSelect` 累积选项，`@StartSelect` 展示选项并等待选择。
- `@Change target=xxx` 切换到下一剧本，并保存当前剧本末尾选择历史。

## 7. 最小复现流程

静态复现：

1. 打开 `scenario/00_z005.ks`。
2. 使用文件开头的片段：

```ks
@Talk name=悠
穹，起床啦！早饭做好了哦！
@Hitret id=671
```

3. 按 `KAGParserCompat._parseKagScript()` 的逻辑解析。
4. 实际 token 只有 `Talk` 和 `Hitret`，没有正文 token。
5. 交给 `ScController.onTag()` 后，`Talk/Hitret` 查不到小写 handler。

运行复现建议：

1. 从标题或调试入口进入 `00_z005`。
2. 观察第一句是否显示。
3. 打开 debug 输出，确认是否出现 `不明なタグです-Talk`、`不明なタグです-Hitret` 等日志。
4. 尝试推进到 `scenario/00_z005.ks:503` 的选项，确认是否能显示选项。
5. 如果第一句已经不能显示，后续选项跳转无需继续测，先修解析层。

## 8. 主要风险

不要直接重写整个解析器。风险如下：

- 剧本规模大，正文约 78,688 组 `Talk/Hitret`，一次性重写容易破坏正常文本、语音、历史、已读和跳转。
- 选项跳转依赖 `_hitretNum`、`_logSelect`、`_stackSelect`、`_logSaveInfo`，不是单纯的 label 跳转。
- 历史回放和普通跳转会调用 `jumpScProcess()` 重放场景状态，解析行为变化会影响截图、回放和保存读取。
- `@if` 条件块在选项前后出现，当前兼容解析器没有条件执行能力，修正文和大小写后仍可能暴露条件分支问题。

建议下一步先做最小兼容修复：

1. tagname 统一小写，但保留 `*label` 原样。
2. 普通正文转换为 `ch` tag。
3. 针对 `Talk -> ch -> Hitret`、`AddSelect -> StartSelect`、`Change` 写离线解析回归。
4. 再评估 `@if/@endif` 是否需要补齐。

## 9. 交接检查项

复现步骤：

- 使用 `scenario/00_z005.ks` 开头的 `@Talk + 正文 + @Hitret` 片段做静态复现。
- 使用 `scenario/00_z005.ks:503-505` 的 `@AddSelect + @StartSelect` 片段做选项复现。

当前实际表现：

- 标签保持首字母大写，无法匹配小写 handler。
- 普通正文不会生成 `ch` tag。

期望表现：

- 标签进入 handler 前归一化。
- 普通正文进入消息显示链路。
- 选项、已读、历史和跳转能收到正确 token。

涉及文件：

- `system/KAGParserCompat.tjs`
- `system/ScController.tjs`
- `system/ADVScreen.tjs`
- `system/MessageFrame.tjs`
- `scenario/*.ks`
- `content-data/scenario/*.ks`
- `content-data/scenario/*.ctx`

下一步建议：

- 先做 tagname 小写化和正文 `ch` token 的最小修复，再补选项跳转回归。
