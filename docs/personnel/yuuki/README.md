# yuuki 责任包

## 职责定位

yuuki 负责代码方向，主负责 `scenario-parser`、`ui-animation`，协助 `engine-compat`、`performance`。

核心目标：

- 修复剧本解析、标签识别、选项跳转和回归测试。
- 设计并实现低风险 UI 动画，不破坏剧情推进和点击响应。
- 配合兼容层负责人确认 KAGParser 相关接口。

## 优先阅读顺序

1. `00-project-overview.md`
2. `01-file-module-split.md`
3. `modules/scenario-parser/README.md`
4. `modules/scenario-parser/tag-support-map.md`
5. `modules/scenario-parser/select-jump-analysis.md`
6. `modules/ui-animation/README.md`
7. `modules/ui-animation/current-animation-map.md`
8. `modules/engine-compat/current-bugs.md`

## 模块目录

- `modules/scenario-parser`
- `modules/ui-animation`
- `modules/engine-compat`
- `modules/performance`

## 主要负责路径

- `scenario/*.ks`
- `content-data/*.ks`
- `content-data/*.ctx`
- `system/KAGParserCompat.tjs`
- `system/ScController.tjs`
- `system/SelectItem.tjs`
- `system/CgFlag.tjs`
- `system/CgModeList.tjs`
- `system/CgSetupInfo.tjs`
- `system/movie.ks`
- `system/AnimationSequence.tjs`
- `system/Action.tjs`
- `system/AffineLayer.tjs`
- `system/Sprite.tjs`
- `system/EnvEffect.tjs`
- `system/EyeCatch.tjs`
- `system/GameSceneManager.tjs`

## 禁止事项

- 不删除剧情段落、选项或跳转来规避解析错误。
- 不替换图像资源。
- 不新增无法退出或无法清理的常驻动画层。
- 不绕过 engine-compat 直接改变兼容层行为。

## 交付要求

- 建立 `.ks` 路线分组、标签、选项、跳转清单。
- 剧本解析问题必须附具体脚本文件和标签位置。
- 动画改动必须写触发条件、清理条件、层数量和降级策略。
- 影响兼容层时先交凝言确认。
