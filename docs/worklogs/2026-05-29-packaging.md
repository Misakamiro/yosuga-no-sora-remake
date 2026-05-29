# 2026-05-29 打包工作日志

## 本轮任务

本轮只创建最终发布包、安装包和用户交付方案文档，不删除源文件，不修改引擎、UI、图像处理文件或剧本内容。

## 已创建文档

- `docs/modules/packaging/README.md`
- `docs/modules/packaging/release-file-list.md`
- `docs/modules/packaging/exclude-list.md`
- `docs/modules/packaging/installer-plan.md`
- `docs/modules/packaging/final-test-plan.md`
- `docs/worklogs/2026-05-29-packaging.md`

## 只读扫描结果

项目路径：

```text
C:\Users\misakamiro\Downloads\Compressed\yosuga_krkrZ\yosuga_krkrZ
```

本轮确认的关键入口：

- `tvpwin32.exe`
- `tvpwin32.cf`
- `startup.tjs`

本轮确认的发布资源目录：

- `icon/`
- `system/`
- `scenario/`
- `bg/`
- `bgm/`
- `se/`
- `frame/`
- `rule/`
- `content-data/`

当前资源统计：

| 目录 | 文件数 | 总大小 |
| --- | ---: | ---: |
| `icon/` | 1 | 210711 bytes |
| `system/` | 65 | 1759463 bytes |
| `scenario/` | 306 | 5759434 bytes |
| `bg/` | 117 | 63217081 bytes |
| `bgm/` | 41 | 45817919 bytes |
| `se/` | 120 | 9611817 bytes |
| `frame/` | 197 | 4696661 bytes |
| `rule/` | 57 | 15856144 bytes |
| `content-data/` | 22430 | 2319429903 bytes |

## 发布包包含什么

发布包应包含：

- 根入口：`tvpwin32.exe`、`tvpwin32.cf`、`startup.tjs`。
- 正式资源：`icon/`、`system/`、`scenario/`、`bg/`、`bgm/`、`se/`、`frame/`、`rule/`、`content-data/`。
- 运行依赖：根目录 `*.dll`、必要 `.cf`、`k2compat.tjs`、`k2compat/`、`font/`。
- 校验文件：`SHA256SUMS.txt`。
- 面向用户的发布说明。

## 排除什么

发布包不得包含：

- `docs/`
- `savedata/`
- `bg_output/`
- `*.log`
- `*.tmp`
- `*.bak`
- `*.old`
- `*.corrupted`
- `*debug*`
- `*test*`
- 任何 0 字节图片

本轮扫描到的重点排除项：

- `bg/B01a.png`：0 字节图片。
- `bg_output/B01a.png`：0 字节临时输出图片。
- `savedata/krkr.console.log`：调试日志。
- `content-data/test_parser.tjs`：测试脚本。
- `content-data/system/debug_path.tjs`：调试脚本。
- `system/*.corrupted`、`content-data/system/*.corrupted`：损坏副本。
- `system/*.bak`、`content-data/**/*.bak`：旧备份。

## 安装测试步骤

安装测试按以下顺序执行：

1. 从源目录生成 `dist/staging/YosugaKrkrZ/`，并应用排除规则。
2. 检查 staging 中入口、配置、脚本、资源目录和运行依赖是否齐全。
3. 检查 staging 中是否仍有日志、临时文件、0 字节图片、`.corrupted`、`.bak`、测试/调试文件。
4. 从 staging 生成便携包并解压到全新临时目录。
5. 从便携目录启动 `tvpwin32.exe`，验证标题、设置、正文、保存、读取、退出。
6. 生成安装包并安装到非开发目录。
7. 通过快捷方式启动，验证工作目录正确。
8. 覆盖安装一次，验证用户存档仍保留。
9. 卸载一次，默认保留存档。
10. 重装并确认旧存档可读取。
11. 计算安装包和便携包 SHA256，写入 `SHA256SUMS.txt`。

## 最终交付格式

建议最终交付：

```text
dist/
  installer/
    YosugaKrkrZ-Setup-<version>.exe
  portable/
    YosugaKrkrZ-Portable-<version>.zip
  checksums/
    SHA256SUMS.txt
  release-notes/
    YosugaKrkrZ-<version>.md
```

## 未执行事项

- 未删除任何文件。
- 未修改 `tvpwin32.exe`、`tvpwin32.cf`、`startup.tjs`。
- 未修改 `system/`、`scenario/`、`bg/`、`bgm/`、`se/`、`frame/`、`rule/`、`content-data/` 的资源内容。
- 未实际生成安装包或便携包。
- 未执行首次启动测试；当前阶段只完成方案和交接文档。
