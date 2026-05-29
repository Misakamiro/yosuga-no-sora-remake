# Packaging 模块说明

## 模块目标

Packaging 模块负责把当前 krkrZ 项目整理成用户可安装、可验证、可卸载的最终交付包。本文档只定义发布方案、文件边界、安装目录、测试流程和交付格式，不直接删除、不重写、不修复运行资源。

## 当前负责范围

本轮交接覆盖以下路径和入口：

- `tvpwin32.exe`
- `tvpwin32.cf`
- `startup.tjs`
- `icon/*`
- `system/*`
- `scenario/*`
- `bg/*`
- `bgm/*`
- `se/*`
- `frame/*`
- `rule/*`
- `content-data/*`

最终发布包还需要携带根目录运行依赖，例如 `*.dll`、必要 `.cf`、兼容脚本和字体目录。它们不属于本轮修改对象，但如果缺失会影响用户启动，因此在发布文件清单中列为运行依赖。

## 不负责范围

Packaging 模块不处理以下内容：

- 不修引擎核心 bug。
- 不修改 UI 代码。
- 不处理、重制、压缩或修复图像资源。
- 不修改剧本内容。
- 不删除调试日志、临时输出、备份或损坏文件，只在清单中标记不得进入发布包。

## 文档索引

- `release-file-list.md`：发布包应包含的文件和目录。
- `exclude-list.md`：不得进入发布包的文件、目录和匹配规则。
- `installer-plan.md`：安装包目录结构、安装/卸载策略、存档保留策略。
- `final-test-plan.md`：首次启动、安装、卸载、重复安装和交付前检查流程。

## 当前只读扫描结论

扫描路径：

`C:\Users\misakamiro\Downloads\Compressed\yosuga_krkrZ\yosuga_krkrZ`

关键结论：

- 根目录存在运行入口 `tvpwin32.exe`、配置 `tvpwin32.cf`、启动脚本 `startup.tjs`。
- 发布主资源目录存在：`icon`、`system`、`scenario`、`bg`、`bgm`、`se`、`frame`、`rule`、`content-data`。
- 项目内存在不应进入发布包的开发/测试/临时内容：`docs`、`savedata`、`bg_output`、`.corrupted`、`.bak`、`debug_path.tjs`、`test_parser.tjs`。
- 当前发现 0 字节图片：`bg/B01a.png`、`bg_output/B01a.png`，发布前必须排除或由图像/资源负责人确认修复。

## 最终交付格式

建议最终交付同时提供：

- 安装包：`dist/installer/YosugaKrkrZ-Setup-<version>.exe`
- 便携包：`dist/portable/YosugaKrkrZ-Portable-<version>.zip`
- 校验文件：`dist/checksums/SHA256SUMS.txt`
- 发布说明：`dist/release-notes/YosugaKrkrZ-<version>.md`
- 安装验证记录：`docs/worklogs/<date>-packaging.md`

