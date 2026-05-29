# 发布文件清单

## 发布包原则

发布包只包含用户运行游戏所需的入口、配置、脚本、资源和运行依赖。开发文档、测试脚本、调试日志、临时输出、旧备份、损坏副本和 0 字节资源不得进入最终包。

## 根目录必须包含

| 路径 | 类型 | 当前用途 | 发布要求 |
| --- | --- | --- | --- |
| `tvpwin32.exe` | 运行入口 | krkrZ 主程序 | 必须包含 |
| `tvpwin32.cf` | 配置 | 当前主配置 | 必须包含 |
| `startup.tjs` | 启动脚本 | 根启动入口 | 必须包含 |
| `bgData.csv` | 数据 | 背景索引数据 | 建议包含，除非运行验证证明已完全迁移到 `system/bgData.csv` |
| `*.dll` | 运行依赖 | KAG、CSV、窗口、图层、视频、音频等扩展 | 必须包含 |
| `*.cf` | 配置 | 兼容/原始配置参考 | 只包含当前运行需要的配置，发布前由启动验证确认 |
| `k2compat.tjs` | 兼容脚本 | krkr2 兼容入口 | 必须包含，除非 engine-compat 明确确认已内联 |
| `k2compat/*` | 兼容脚本 | 兼容层辅助脚本 | 必须包含，除非 engine-compat 明确确认不再加载 |
| `font/*` | 字体 | 游戏字体资源 | 建议包含，除非运行验证证明不再引用 |

当前根目录运行依赖清单：

- `csvParser.dll`
- `extrans.dll`
- `fstat.dll`
- `KAGParser.dll`
- `KAGParserEx.dll`
- `krmovie.dll`
- `layerExDraw.dll`
- `menu.dll`
- `saveStruct.dll`
- `win32dialog.dll`
- `windowEx.dll`
- `wuvorbis.dll`

## 资源目录必须包含

| 目录 | 当前文件数 | 当前总大小 | 发布要求 |
| --- | ---: | ---: | --- |
| `icon/` | 1 | 210711 bytes | 必须包含 |
| `system/` | 65 | 1759463 bytes | 包含正式 `.tjs`、`.csv`、`.ks`，排除 `.bak` 和 `.corrupted` |
| `scenario/` | 306 | 5759434 bytes | 必须包含 |
| `bg/` | 117 | 63217081 bytes | 包含正式背景，排除 0 字节图片 |
| `bgm/` | 41 | 45817919 bytes | 必须包含 |
| `se/` | 120 | 9611817 bytes | 必须包含 |
| `frame/` | 197 | 4696661 bytes | 必须包含 |
| `rule/` | 57 | 15856144 bytes | 必须包含 |
| `content-data/` | 22430 | 2319429903 bytes | 必须包含正式内容，排除调试、测试、备份、损坏副本 |

## 建议发布目录树

```text
YosugaKrkrZ/
  tvpwin32.exe
  tvpwin32.cf
  startup.tjs
  bgData.csv
  *.dll
  *.cf
  k2compat.tjs
  k2compat/
  font/
  icon/
  system/
  scenario/
  bg/
  bgm/
  se/
  frame/
  rule/
  content-data/
```

## 发布前必须确认

- `tvpwin32.exe` 能在干净发布目录中启动。
- `startup.tjs` 加载链不依赖开发目录、绝对路径或 `savedata`。
- `system/` 和 `content-data/system/` 中正式脚本没有误用 `.corrupted` 或 `.bak`。
- 0 字节文件不进入候选包。
- `docs/`、`savedata/`、`bg_output/` 不进入用户包。
- 生成 `SHA256SUMS.txt` 后，不再修改发布包内容；如修改，必须重新生成校验。
