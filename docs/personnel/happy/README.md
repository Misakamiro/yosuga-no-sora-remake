# happy 责任包

## 职责定位

happy 负责人工和发布方向，主负责 `packaging`、`image-qa` 和功能验收。

核心目标：

- 建立最终安装包、便携包和校验文件流程。
- 阻止未通过 QA 的图像、脚本、调试文件进入用户包。
- 完成安装、启动、卸载、覆盖安装和存档保留验证。

## 优先阅读顺序

1. `00-project-overview.md`
2. `01-file-module-split.md`
3. `modules/packaging/README.md`
4. `modules/packaging/release-file-list.md`
5. `modules/packaging/exclude-list.md`
6. `modules/packaging/final-test-plan.md`
7. `modules/image-qa/README.md`
8. `modules/image-qa/qa-checklist.md`

## 模块目录

- `modules/packaging`
- `modules/image-qa`

## 主要负责路径

- `dist/installer/*`
- `dist/portable/*`
- `dist/checksums/*`
- `tools/packaging/*`
- `icon/*.ico`
- `icon_change_instructions.txt`
- `work/image-remaster/qa/*`

## 禁止事项

- 不把 `savedata/` 打进发布包。
- 不发布未通过 QA 的图像或脚本。
- 不直接修改代码临时绕过打包问题。
- 不从开发根目录直接生成最终发布包，必须先经过 staging。

## 交付要求

- 安装包必须写清包含文件、排除文件、SHA256。
- 每个候选包必须做安装、启动、卸载验证。
- 图像 QA 标清严重等级：阻塞、高、中、低。
- 人工验收记录必须对应具体版本和包路径。
