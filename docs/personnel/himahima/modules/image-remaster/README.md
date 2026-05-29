# 图像资源 16:9 扩图与高清化

## 模块目标

本模块负责把现有 KRKR 图像资源盘点、分组，并制定一套不覆盖原图的 16:9 扩图与高清化交接标准。当前阶段只创建流程文档，不生成、覆盖或替换任何图片资源。

项目根目录：

`C:\Users\misakamiro\Downloads\Compressed\yosuga_krkrZ\yosuga_krkrZ`

独立输出根目录：

`C:\Users\misakamiro\Downloads\Compressed\yosuga_krkrZ\yosuga_krkrZ\remaster-output\image-remaster`

## 负责范围

- `bg/*`
- `content-data/bg/*`
- `content-data/char/*`
- `content-data/thumb/*`
- `frame/*`
- `content-data/frame/*`
- `rule/*`
- `content-data/rule/*`
- 其他 CG / event / char 资源按实际盘点结果处理；本次未发现单独的 `cg` 或 `event` 顶层目录，相关事件图层主要混在 `content-data/char`。

## 禁止修改

- 代码文件，例如 `.tjs`、`.ks`、`.dll`、`.exe`
- 剧本文件，例如 `scenario/*`
- 原始图像资源，例如 `.tlg`、`.png`、`.bmp`、`.ico`
- 打包文件或运行时文件

所有试制、修图、放大和 QA 标记必须写入 `remaster-output/image-remaster` 下的阶段目录。

## 输出尺寸原则

- 背景 / CG：最终交付 `1920x1080`
- UI 图：按 UI 组需求，不默认强行拉到 `1920x1080`
- 缩略图：按系统窗口需求；在 UI 组未确认前，先保留兼容尺寸记录，并准备 16:9 预览尺寸方案
- 转场图：按目标分辨率与引擎调用方式确认；全屏转场可做 `1920x1080`，小型遮罩或规则贴图不得无依据重采样

## 交接文件

- `asset-inventory.md`：资源盘点、类型、格式、尺寸分布
- `processing-standard.md`：备份、NovelAI 扩图、PS 修图、waifu2x、QA、返工标准
- `sample-list.md`：流程验证样张、参数、输出路径
- `output-naming-standard.md`：阶段目录与命名规则
- `docs/worklogs/2026-05-29-image-remaster.md`：本次工作记录

