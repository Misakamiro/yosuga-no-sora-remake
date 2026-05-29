# 2026-05-29 UI 重做工作日志

## 任务

为 `1920x1080` 现代化 UI 重做创建交接文档。要求输出文件：

- `docs/modules/ui-redesign/README.md`
- `docs/modules/ui-redesign/screen-map.md`
- `docs/modules/ui-redesign/asset-request-list.md`
- `docs/modules/ui-redesign/layout-standard.md`
- `docs/worklogs/2026-05-29-ui-redesign.md`

## 已检查文件

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
- `system/Status.tjs`，用于确认当前窗口基准尺寸

## 发现

- 当前 UI 基于 `800x600` 4:3 坐标。
- 目标 UI 应按 `1920x1080` 原生设计，简单缩放会保留旧 4:3 密度，并浪费 16:9 空间。
- 多数全屏页面已经使用 `WINDOW_WIDTH` 和 `WINDOW_HEIGHT`，但内部面板、卡片、文字区域和按钮仍大量使用固定旧坐标。
- 大多数 UI 位图资源使用 `frame` 目录下的 `FRM_*` 命名。
- CG 和回忆缩略图分布在 `frame` 和 `thumb`；其中 `FRM_06R21` 之后位于 `thumb`。
- 现有字体资源是 `.tft` 尺寸梯度，但中文可读性需要实现前验证。

## 已创建文档

- `README.md`：总体范围、页面盘点、设计方向、交接文件索引。
- `screen-map.md`：逐页面负责文件、当前资源、当前问题、目标 `1920x1080` 布局和素材需求。
- `layout-standard.md`：1080p 坐标、字号、色彩、交互和页面布局标准。
- `asset-request-list.md`：按优先级整理的图片组交付清单。

## 验证

- 已确认要求的文档路径存在。
- 已检查新 Markdown 文件包含预期标题，且没有未完成占位标记。
- 未修改引擎兼容文件、剧本解析器、原始美术资源或封包文件。
