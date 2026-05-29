# 剧本解析回归测试计划

日期：2026-05-29

目标：在不重写整个解析器的前提下，逐步证明 `.ks/.ctx` 解析、普通正文显示、选项跳转、已读状态和历史回放没有回退。

## 1. 测试原则

- 先离线验证 token，再进入游戏验证表现。
- 每次只修一个解析能力，避免同时改大小写、正文、条件分支和跳转状态。
- 所有测试都记录实际表现和期望表现。
- 不修改图像资源、UI 视觉资源、打包文件。

## 2. 基线复现

用例：`scenario/00_z005.ks` 开头第一句。

片段：

```ks
@Talk name=悠
穹，起床啦！早饭做好了哦！
@Hitret id=671
```

当前实际表现：

- 解析 token 只有 `Talk`、`Hitret`，没有正文 token。
- Handler 需要 `talk`、`hitret`。
- 正文不会进入 `MessageFrame.output()`。

期望表现：

- token 顺序应至少等价于 `talk -> ch -> hitret`。
- 第一句显示在消息框。
- 点击后继续下一句。
- `id=671` 被标记已读。
- 历史里出现该句。

## 3. 离线解析测试

建议新增轻量测试脚本或调试入口，不直接依赖画面资源。

### Case A：标签大小写

输入：

```ks
@Talk name=悠 voice=TEST001
文本
@Hitret id=1
```

期望 token：

```text
talk name=悠 voice=TEST001
ch text=文本
hitret id=1
```

### Case B：正文连续行

输入：

```ks
@Talk name=心の声
第一行
第二行
@Hitret id=2
```

期望：

- 两行正文都进入消息。
- 是否合并为一个 `ch` 或多个 `ch` 可以由实现决定，但显示顺序必须一致。

### Case C：选项

输入：

```ks
@AddSelect text=敲门 hint=穹
@AddSelect text=今天就先这样吧 hint=奈緒/瑛/一葉/初佳
@StartSelect
```

期望：

- 两个 `addselect` token。
- 一个 `startselect` token。
- `_select.count == 2` 后再进入 `startSelect()`。

### Case D：剧本切换

输入：

```ks
@Change target=00_a005
```

期望：

- token 为 `change target=00_a005`。
- handler 调用 `change("00_a005", void, true)`。

### 用例 E：SelectTerminate

输入：

```ks
@SelectTerminate
```

期望：

- token 为 `selectterminate`。
- `SELECTTERMINATE_FLAG` 被设置。

## 4. 游戏内手动回归

### Case 1：普通正文显示

复现步骤：

1. 从标题或调试入口进入 `00_z005`。
2. 查看第一句是否显示。
3. 点击推进三句。
4. 打开历史。

期望表现：

- 第一至第三句正常显示。
- 每次 `Hitret` 等待点击。
- 历史含对应文本。

当前实际表现：

- 原始状态下预计不会显示正文，或日志出现未知标签。

涉及文件：

- `system/KAGParserCompat.tjs`
- `system/ScController.tjs`
- `system/ADVScreen.tjs`
- `system/MessageFrame.tjs`
- `scenario/00_z005.ks`

### Case 2：第一个选项

复现步骤：

1. 进入 `00_z005`。
2. 推进到 `scenario/00_z005.ks:503-505`。
3. 确认显示两个选项。
4. 点击“敲门”。

期望表现：

- 出现两个选项。
- 点击后 `_ret_select == 1`。
- 历史追加“选择了《敲门》选项。”。
- `_stackSelect.count` 增加。

当前实际表现：

- 原始状态下 `AddSelect/StartSelect` 大小写不匹配，选项不会正常出现。

涉及文件：

- `system/KAGParserCompat.tjs`
- `system/ADVScreen.tjs`
- `scenario/00_z005.ks`

### Case 3：跳转下一选项

复现步骤：

1. 进入一个已经正常阅读过的存档。
2. 在普通消息等待状态打开跳转菜单。
3. 点击“下一选项”。
4. 确认后观察停点。

期望表现：

- 如果后续内容已读，跳到下一个 `StartSelect`。
- 如果配置为仅已读跳过且遇到未读 `Hitret`，停在未读句子。
- 到达选项后显示选项 UI。

当前实际表现：

- 原始状态下 `_hitretState` 和 `startselect` 扫描都不可靠。

涉及文件：

- `system/ADVScreen.tjs`
- `system/MessageFrame.tjs`
- `system/KAGParserCompat.tjs`

### 用例 4：SelectTerminate

复现步骤：

1. 进入 `00_z014`。
2. 推进到 `scenario/00_z014.ks:522-526`。
3. 选择后继续到 `@SelectTerminate`。
4. 打开跳转菜单。

期望表现：

- `SELECTTERMINATE_FLAG` 设置后，“下一选项”不可用。

当前实际表现：

- 原始状态下 `SelectTerminate` 不匹配 handler，终止标记不会设置。

### Case 5：Change 跨文件

复现步骤：

1. 进入 `00_a004`。
2. 推进到文件末尾 `@Change target=00_a005`。
3. 观察是否进入 `00_a005`。

期望表现：

- 当前剧本选择历史保存到 `_logSelectEOF`。
- `_scenario` 切到 `00_A005`。
- 新文件第一句正常显示。

当前实际表现：

- 原始状态下 `Change` 不匹配 handler，跨文件切换不会执行。

## 5. 历史回放回归

复现步骤：

1. 正常阅读至少 5 句。
2. 打开历史窗口。
3. 选择第一句历史跳回。
4. 再选择最后一句历史跳回。

期望表现：

- 画面状态按目标 `hitret` 恢复。
- 最后一条消息文本、说话人、语音状态正确。
- 回放不新增错误选项历史。

当前风险：

- `jumpNormalWork()` 依赖 `_logSaveInfo`、`_logSelect`、`jumpScProcess()`。
- 如果解析器 token 不稳定，历史回放会跟着不稳定。

## 6. 不建议一次性覆盖的范围

暂不建议第一轮就覆盖：

- 完整 KAG 条件语法。
- 宏递归高级写法。
- 多行标签和复杂转义。
- 所有视觉/演出标签行为。

原因：当前第一断点在标签大小写和正文 token。先修这两个，才能看清下一层真实问题。

## 7. 下一步建议

执行顺序：

1. 先建立离线 token 测试，锁定当前失败基线。
2. 修复 tagname 小写化。
3. 修复普通正文 `ch` token。
4. 再跑游戏内普通正文、选项、下一选项、历史回放和跨文件 `Change` 回归。
