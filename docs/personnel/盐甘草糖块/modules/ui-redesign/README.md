# 1920x1080 UI 重做交接

## 范围

本模块记录 KRKRZ 项目的 UI 重做方案。当前 UI 基于 `system/Status.tjs` 中定义的 `800x600` 4:3 坐标，目标是制作原生 `1920x1080` 16:9 UI，而不是把旧 4:3 皮肤直接拉伸。

允许 UI 重做实现人员处理的文件：

- `system/Title.tjs`
- `system/MessageFrame.tjs`
- `system/MessageArea.tjs`
- `system/SelectItem.tjs`
- `system/Confirm.tjs`
- `system/ConfigWindow.tjs`
- `system/SystemWindow.tjs`
- `system/Album.tjs`
- `frame/*`
- `thumb/*`
- `font/*`

不在本模块范围内：

- 引擎兼容层。
- 剧本解析器。
- 原始背景、CG、角色立绘。
- 打包归档和发布包文件。

## 当前 UI 盘点

| UI 页面 | 主要代码文件 | 当前主要资源 | 当前基准尺寸 | 目标布局 |
| --- | --- | --- | --- | --- |
| 标题 | `system/Title.tjs` | `FRM_0511`、`FRM_0551`-`FRM_0556`、`FRM_0561`-`FRM_0563` | `800x600` | 完整 `1920x1080` 标题场景，菜单放在右侧或右下安全区域 |
| 对话框 | `system/MessageFrame.tjs`、`system/MessageArea.tjs` | `FRM_0101`、`FRM_0102`、`FRM_0105`、`FRM_0106`、`FRM_0108`、`FRM_0109`、`FRM_0161`-`FRM_0168` | 对话框约 `750x170` | 底部居中对话面板，建议约 `1560x210`，避免遮挡主要画面 |
| 快捷菜单 | `system/MessageFrame.tjs`、`system/SelectItem.tjs` | `FRM_0122`-`FRM_0129`、`FRM_0130`-`FRM_0133`、`FRM_0141`、`FRM_0142`、`FRM_0181`-`FRM_0183` | 800x600 下的菜单条和子菜单 | 右下或右上紧凑工具条，扩大点击区 |
| 选项 | `system/ADVScreen.tjs`、`system/SelectItem.tjs` | 运行时按钮和选择项基础控件 | 800x600 居中 | `1920x1080` 居中竖排选项，最大宽度约 `920` |
| 设置 | `system/ConfigWindow.tjs` | `FRM_02A01`-`FRM_02A04`、`FRM_02A11`-`FRM_02A42`、`FRM_02B01`-`FRM_02C11`、`FRM_1101` | `640x568` 设置面板 | `1440x860` 居中设置界面，左侧标签，右侧内容 |
| 存档 | `system/SystemWindow.tjs` | `FRM_0302`、`FRM_0303s`、`FRM_0304`-`FRM_0346`、`FRM_1101` | 全屏 `800x600`，存档卡 `168x188`，3x3 | `1600x900` 存档浏览器，4x2 或 4x3 |
| 读档 | `system/SystemWindow.tjs` | `FRM_0301`、`FRM_0303l`、`FRM_0304`-`FRM_0346`、`FRM_1101` | 与存档相同 | 与存档一致的网格，使用偏蓝或中性色强调 |
| 历史 | `system/SystemWindow.tjs`、`system/MessageArea.tjs` | `FRM_0401`、`FRM_0402`、`FRM_0411`-`FRM_0414`、`FRM_0109`、`FRM_1101` | 全屏 `800x600`，文本列约 `720` 宽 | `1500x900` 可读日志面板，右侧滚动轨，语音/跳转按钮内联 |
| CG 模式 | `system/Album.tjs` | `FRM_06C01`-`FRM_06C42`、`thumb/*` | 全屏 `800x600`，3x3 缩略图 | `1920x1080` 图库，左侧分类栏，4x3 或 5x2 缩略图 |
| 确认弹窗 | `system/Confirm.tjs` | `FRM_0701`、`FRM_0711`、`FRM_0712`、`FRM_02A31`、`FRM_0751` | `800x160` 弹窗，`800x48` 提示条 | `720x260` 居中模态框，带暗化遮罩和明确主/次按钮 |

## 设计方向

新的 UI 应该更现代、清楚、克制。需要可读性时使用半透明深色或暖中性色面板，尽量让原作画面保持可见。UI 应靠近边缘安全区，避免在角色和背景主体区域放大块不透明面板。

核心规则：

- 使用原生 `1920x1080` 坐标设计。
- 不拉伸旧 `800x600` 面板，核心框架素材应按目标尺寸重做。
- 点击目标放大：普通按钮至少 `160x48`，图标按钮至少 `48x48`。
- 1080p 下文字要清晰：正文 `32-36px`，标签 `24-28px`，标题 `40-56px`。
- 必须区分正常、悬停/焦点、按下、禁用、选中、锁定状态。
- 标题、设置、存档/读档、历史、Album 保持统一视觉语言。

## 交接文件

- `screen-map.md`：各页面的文件、资源、布局映射。
- `layout-standard.md`：`1920x1080` 间距、字号、交互和状态规范。
- `asset-request-list.md`：图片组需要交付的 UI 素材清单。
- `docs/worklogs/2026-05-29-ui-redesign.md`：本轮 UI 文档工作日志。
