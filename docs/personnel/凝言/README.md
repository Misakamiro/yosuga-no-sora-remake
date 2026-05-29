# 凝言责任包

## 职责定位

凝言负责代码和 UI 方向，主负责 `engine-compat`、`ui-redesign`，协助 `ui-animation`、`image-remaster`、`packaging`。

核心目标：

- 修稳定 krkr2 到 krkrZ 的兼容启动链。
- 推进 `1920x1080` UI 重做的脚本侧实现。
- 在图像回灌和打包前确认 UI 资源引用、依赖和运行入口。

## 优先阅读顺序

1. `00-project-overview.md`
2. `01-file-module-split.md`
3. `modules/engine-compat/README.md`
4. `modules/engine-compat/current-bugs.md`
5. `modules/ui-redesign/README.md`
6. `modules/ui-redesign/screen-map.md`
7. `modules/ui-animation/animation-upgrade-plan.md`
8. `templates/04-handoff-template.md`

## 模块目录

- `modules/engine-compat`
- `modules/ui-redesign`
- `modules/ui-animation`
- `modules/image-remaster`
- `modules/packaging`

## 主要负责路径

- `startup.tjs`
- `k2compat.tjs`
- `k2compat/*.tjs`
- `system/KAGParserCompat.tjs`
- `system/csvParserCompat.tjs`
- `system/GdiPlusCompat.tjs`
- `system/win32dialog.tjs`
- `system/Initialize.tjs`
- `system/Status.tjs`
- `system/System.tjs`
- `system/SystemWindow.tjs`
- `system/Title.tjs`
- `system/ConfigWindow.tjs`
- `system/MessageFrame.tjs`
- `system/MessageArea.tjs`
- `frame/*.tlg` 的 UI 回灌协调

## 禁止事项

- 不直接改 `scenario/*.ks` 的剧情内容。
- 不删除 `.bak`、`.corrupted` 文件。
- 不把未通过 QA 的图像覆盖到正式资源目录。
- 不替 happy 直接发布安装包。

## 交付要求

- 每次兼容层修改写清影响的 API、类、函数和加载顺序。
- UI 修改必须附 `1920x1080` 截图。
- 图像回灌必须引用 `image-qa` 通过清单。
- 任何跨模块修改必须填写 `templates/04-handoff-template.md`。
