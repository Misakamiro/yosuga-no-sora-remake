# 排除清单

## 排除原则

以下内容不得进入用户安装包或便携包：

- 调试日志。
- 临时输出。
- 0 字节图片。
- `.corrupted` 文件。
- 开发文档。
- 测试脚本。
- 旧备份。
- 当前用户存档和本机运行痕迹。

本清单是打包规则，不是删除清单。不要在源目录直接删除这些文件。

## 必须排除的目录

| 路径 | 原因 |
| --- | --- |
| `docs/` | 开发文档和交接记录，不面向用户发布 |
| `savedata/` | 当前机器存档、截图和运行日志，不能污染用户初始状态 |
| `bg_output/` | 临时图像输出目录 |
| `dist/` | 打包产物目录本身，避免递归打包 |
| `tools/` | 打包脚本或开发工具目录，如后续创建则不得进入用户包 |
| `work/` | 临时工作目录，如后续创建则不得进入用户包 |

## 必须排除的文件模式

```text
*.log
*.tmp
*.bak
*.old
*.corrupted
*debug*
*test*
```

发布脚本还必须检查所有文件大小，任何 `Length == 0` 的图片资源不得进入发布包。

## 当前扫描到的具体排除项

### 0 字节图片

```text
bg/B01a.png
bg_output/B01a.png
```

### 调试日志

```text
savedata/krkr.console.log
```

### 测试脚本

```text
content-data/test_parser.tjs
```

### 调试脚本

```text
content-data/system/debug_path.tjs
```

### 旧备份

```text
content-data/begin.tjs.bak
content-data/startup.tjs.bak
content-data/system/system/EnvEffect.tjs.bak
content-data/system/system/Initialize.tjs.bak
content-data/system/system/ScController.tjs.bak
system/AnimationSequence.tjs.bak
system/GameSceneManager.tjs.bak
```

### 损坏副本

```text
content-data/system/ADVObject.tjs.corrupted
content-data/system/ADVScreen.tjs.corrupted
content-data/system/Album.tjs.corrupted
content-data/system/ConfigWindow.tjs.corrupted
content-data/system/MessageFrame.tjs.corrupted
content-data/system/Movie.tjs.corrupted
content-data/system/SelectItem.tjs.corrupted
content-data/system/Sound.tjs.corrupted
content-data/system/System.tjs.corrupted
content-data/system/SystemWindow.tjs.corrupted
content-data/system/Title.tjs.corrupted
system/ADVObject.tjs.corrupted
system/ADVScreen.tjs.corrupted
system/Album.tjs.corrupted
system/ConfigWindow.tjs.corrupted
system/MessageFrame.tjs.corrupted
system/Movie.tjs.corrupted
system/SelectItem.tjs.corrupted
system/Sound.tjs.corrupted
system/System.tjs.corrupted
system/SystemWindow.tjs.corrupted
system/Title.tjs.corrupted
```

## 打包前排除检查命令

在项目根目录执行：

```powershell
$root = "C:\Users\misakamiro\Downloads\Compressed\yosuga_krkrZ\yosuga_krkrZ"
Get-ChildItem -LiteralPath $root -Recurse -Force -File |
  Where-Object {
    $_.Length -eq 0 -or
    $_.Name -like "*.corrupted" -or
    $_.Name -like "*.bak" -or
    $_.Name -like "*.log" -or
    $_.Name -like "*.tmp" -or
    $_.Name -match "(?i)test|debug|backup|old"
  } |
  Select-Object FullName, Length
```

预期结果：

- 源目录可以返回上面列出的候选排除项。
- 打包输出目录必须返回空结果。
