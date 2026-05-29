# 最终测试计划

## 测试目标

验证最终安装包和便携包满足以下条件：

- 包含运行所需入口、配置、脚本、资源和依赖。
- 不包含调试日志、临时输出、0 字节图片、`.corrupted`、开发文档、测试脚本和旧备份。
- 全新安装后可以首次启动。
- 卸载默认保留用户存档。
- 最终交付物带有可复核的 SHA256 校验。

## 测试环境

至少执行两类测试：

- 干净目录便携启动测试：从 `dist/portable` 解压到新目录运行。
- 安装器测试：安装到非开发目录运行。

建议测试路径：

```text
C:\Users\misakamiro\AppData\Local\Temp\YosugaKrkrZ-portable-smoke\
C:\Users\misakamiro\AppData\Local\Programs\YosugaKrkrZ\
```

## 打包前文件检查

在 staging 目录执行：

```powershell
$stage = "C:\Users\misakamiro\Downloads\Compressed\yosuga_krkrZ\yosuga_krkrZ\dist\staging\YosugaKrkrZ"

Test-Path "$stage\tvpwin32.exe"
Test-Path "$stage\tvpwin32.cf"
Test-Path "$stage\startup.tjs"
Test-Path "$stage\icon"
Test-Path "$stage\system"
Test-Path "$stage\scenario"
Test-Path "$stage\bg"
Test-Path "$stage\bgm"
Test-Path "$stage\se"
Test-Path "$stage\frame"
Test-Path "$stage\rule"
Test-Path "$stage\content-data"
```

预期结果：

- 每一项返回 `True`。

检查排除项：

```powershell
Get-ChildItem -LiteralPath $stage -Recurse -Force -File |
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

- 无输出。

## 首次启动测试流程

1. 复制或安装到全新目录。
2. 确认目录内不存在开发文档、旧 `savedata`、临时输出。
3. 双击或命令行启动：

```powershell
Start-Process -FilePath "<InstallDir>\tvpwin32.exe" -WorkingDirectory "<InstallDir>"
```

4. 等待标题画面出现。
5. 进入设置画面，确认能打开和返回。
6. 开始游戏，确认能进入正文第一段。
7. 播放到一次背景、BGM、SE 出现的位置，确认无缺资源弹窗。
8. 打开存档页，执行一次保存。
9. 退出游戏。
10. 重新启动游戏，确认可以读到刚才保存的存档。

通过标准：

- 启动过程中没有缺 DLL、缺脚本、缺资源、语法错误或路径错误弹窗。
- 标题、设置、正文、保存、读取、退出路径可完成。
- 新生成的存档位于预期用户数据目录，或已记录当前引擎仍写入安装目录的限制。

## 安装器测试流程

1. 运行 `YosugaKrkrZ-Setup-<version>.exe`。
2. 安装到测试目录。
3. 确认开始菜单快捷方式存在。
4. 通过快捷方式启动，确认工作目录正确。
5. 执行首次启动测试流程。
6. 关闭游戏。
7. 重新运行安装器覆盖安装。
8. 启动游戏，确认旧存档仍在。

通过标准：

- 安装器没有把 `docs/`、`savedata/`、`bg_output/` 带入安装目录。
- 快捷方式可以启动游戏。
- 覆盖安装不删除用户存档。

## 卸载测试流程

1. 确认已经通过安装器生成至少一个存档。
2. 运行卸载器。
3. 保持“删除用户存档和设置”选项关闭。
4. 完成卸载。
5. 确认程序文件和快捷方式已移除。
6. 确认用户存档仍保留。
7. 再次安装同版本。
8. 启动游戏，确认旧存档仍可读取。

通过标准：

- 默认卸载不删除用户存档。
- 重装后存档可继续读取。
- 只有用户明确选择删除存档时，才清理用户数据目录。

## 便携包测试流程

1. 解压 `YosugaKrkrZ-Portable-<version>.zip` 到全新临时目录。
2. 确认解压目录只有发布清单内文件。
3. 从解压目录启动 `tvpwin32.exe`。
4. 执行首次启动测试流程。
5. 删除临时目录前确认没有残留进程。

通过标准：

- 便携包不需要安装即可运行。
- 解压目录不包含开发文件和排除项。

## 校验测试

在 `dist` 目录执行：

```powershell
Get-FileHash .\installer\YosugaKrkrZ-Setup-<version>.exe -Algorithm SHA256
Get-FileHash .\portable\YosugaKrkrZ-Portable-<version>.zip -Algorithm SHA256
```

将结果写入：

```text
dist/checksums/SHA256SUMS.txt
```

通过标准：

- 发布说明中的 SHA256 与 `SHA256SUMS.txt` 一致。
- 交付文件上传或复制后重新计算，哈希仍一致。
