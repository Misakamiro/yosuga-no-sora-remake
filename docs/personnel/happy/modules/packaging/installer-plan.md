# 安装器计划

## 交付物

最终建议提供两种用户交付格式：

- 安装版：`YosugaKrkrZ-Setup-<version>.exe`
- 便携版：`YosugaKrkrZ-Portable-<version>.zip`

两种格式都必须附带：

- `SHA256SUMS.txt`
- 发布说明。
- 安装/启动/卸载验证记录。

## 构建输出目录

```text
dist/
  staging/
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
  portable/
    YosugaKrkrZ-Portable-<version>.zip
  installer/
    YosugaKrkrZ-Setup-<version>.exe
  checksums/
    SHA256SUMS.txt
  release-notes/
    YosugaKrkrZ-<version>.md
```

`dist/staging` 是唯一允许被安装器和便携包读取的发布候选目录。不要从开发根目录直接生成安装包。

## 安装目录结构

默认安装位置：

```text
%ProgramFiles%\YosugaKrkrZ\
```

用户可选安装位置：

```text
%LOCALAPPDATA%\Programs\YosugaKrkrZ\
```

安装后目录：

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

## 快捷方式

安装器应创建：

- 开始菜单快捷方式：`YosugaKrkrZ`
- 可选桌面快捷方式：`YosugaKrkrZ`

快捷方式目标：

```text
<InstallDir>\tvpwin32.exe
```

工作目录必须设置为：

```text
<InstallDir>
```

## 用户数据目录

存档、配置和日志不得写入安装包源目录。建议保留在用户数据目录：

```text
%APPDATA%\YosugaKrkrZ\
  savedata/
  config/
  logs/
```

如果当前引擎仍写入安装目录下的 `savedata/`，安装测试必须记录这个限制，并在卸载策略中保留 `savedata/`。

## 卸载策略

卸载器默认删除：

- 安装目录内由安装器写入的程序文件。
- 开始菜单快捷方式。
- 用户选择创建的桌面快捷方式。

卸载器默认保留：

- `%APPDATA%\YosugaKrkrZ\savedata/`
- `%APPDATA%\YosugaKrkrZ\config/`
- 安装目录内用户运行后生成的 `savedata/`

卸载器可以提供一个明确选项：

```text
同时删除用户存档和设置
```

该选项必须默认关闭。

## 重复安装和升级策略

- 安装新版本前不删除用户存档。
- 覆盖程序文件前先关闭正在运行的 `tvpwin32.exe`。
- 升级安装后必须重新启动一次游戏，确认旧存档仍可读取。
- 如果运行目录仍包含旧版本遗留文件，安装器不得静默删除未知用户文件；应只覆盖发布清单内的文件。

## 打包规则

- 从 `dist/staging/YosugaKrkrZ` 生成安装包和便携包。
- 所有排除规则先应用到 staging，再生成交付物。
- 安装包生成后计算 SHA256。
- 便携包生成后计算 SHA256。
- 校验文件生成后不再修改交付物。
