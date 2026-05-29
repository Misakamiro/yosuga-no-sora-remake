# 2026-05-29 图像资源 16:9 扩图与高清化工作记录

## 工作范围

项目路径：

`C:\Users\misakamiro\Downloads\Compressed\yosuga_krkrZ\yosuga_krkrZ`

本次只做文档和只读盘点：

- 盘点图像目录和格式
- 解析 TLG / PNG 尺寸
- 制定处理流程
- 规定输出目录和命名标准
- 选择 5 张流程验证样张

未执行：

- 未运行 NovelAI
- 未运行 Photoshop
- 未运行 waifu2x
- 未生成最终图片
- 未覆盖任何原始图像资源
- 未修改代码、剧本、打包文件

## 盘点结论

主要图像资源：

- `.tlg`：4816 个
- `.png`：116 个
- `.bmp`：7 个
- `.ico`：1 个

重点目录：

- `bg`：116 个 `.tlg`，另有 0 字节 `B01a.png`
- `content-data/bg`：116 个 `.tlg`
- `content-data/char`：2288 个 `.tlg`
- `frame`：197 个 `.tlg`
- `content-data/frame`：197 个 `.tlg`
- `thumb`：951 个 `.tlg`
- `content-data/thumb`：951 个 `.tlg`
- `rule`：57 个 `.png`
- `content-data/rule`：57 个 `.png`

未发现独立 `cg` 或 `event` 顶层目录。CG / event 候选资源集中在 `content-data/char`，该目录同时混有人物层、事件图层、背景副本和 UI 副本。

## 样张路径

| 编号 | 类型 | 源路径 | 源尺寸 | 目标 |
| --- | --- | --- | --- | --- |
| S01 | 背景 | `bg/B01a.tlg` | `800x600` | `1920x1080` |
| S02 | CG / event 候选 | `content-data/char/SP01.tlg` | `800x600` | `1920x1080` |
| S03 | 立绘 / 人物层候选 | `content-data/char/cc01_01l.tlg` | `714x738` | 按调用方式确认 |
| S04 | UI 图 | `frame/FRM_02A02.tlg` | `640x568` | 按 UI 组需求 |
| S05 | 缩略图 | `content-data/thumb/ca01_01TS.tlg` | `100x75` | 按系统窗口需求 |

转场代表文件已登记但未纳入 5 张主样张：

- `rule/CLOUD_A.png`，`800x600`
- `rule/curtain_close.png`，`800x600`
- `rule/shutter_open.png`，`800x600`

## 使用工具

本次实际使用：

- PowerShell：目录和扩展名盘点
- PowerShell + TLG 头部读取：读取 TLG 宽高
- `System.Drawing.Image`：读取 PNG 宽高

后续处理规定工具：

- TLG 解码器：GARbro / KrkrExtract / 可读取 TLG5/TLG6 的转换器
- NovelAI：16:9 outpainting / image-to-image
- Adobe Photoshop：人工修图
- waifu2x-caffe / waifu2x-ncnn-vulkan：放大和去噪
- ImageMagick 或同类工具：最终尺寸检查

## 参数

背景 / CG：

- NovelAI target canvas：`1920x1080`
- NovelAI source preservation：`0.50-0.70`
- NovelAI denoise / strength：`0.30-0.50`
- Seed：固定并记录
- waifu2x model：anime style
- waifu2x noise：`1`
- waifu2x scale：`2x`

UI：

- 不默认 16:9 化
- 保留 alpha
- 按 UI 组确认尺寸
- waifu2x noise：`0-1`
- waifu2x scale：`2x` 仅用于边缘质量验证

缩略图：

- 从最终图重新生成
- 未确认系统窗口前，候选尺寸为 `320x180` 和 `480x270`
- 保留原始缩略图尺寸记录

## 输出路径

统一输出根目录：

`C:\Users\misakamiro\Downloads\Compressed\yosuga_krkrZ\yosuga_krkrZ\remaster-output\image-remaster`

样张最终输出路径规划：

- `remaster-output/image-remaster/05-final/bg/B01a__bg__1920x1080__v001.png`
- `remaster-output/image-remaster/05-final/cg/SP01__cg__1920x1080__v001.png`
- `remaster-output/image-remaster/05-final/char/cc01_01l__char-layer__native-or-approved__v001.png`
- `remaster-output/image-remaster/05-final/ui/FRM_02A02__ui__approved-size__v001.png`
- `remaster-output/image-remaster/05-final/thumb/ca01_01TS__thumb__320x180__v001.png`

QA 记录路径规划：

`remaster-output/image-remaster/06-qa/2026-05-29/<asset-stem>__qa.md`

## 返工标准

以下情况必须返工：

- 输出尺寸不符合目标或未记录审批原因
- 原始资源被覆盖、移动或删除
- NovelAI 生成额外人物、文字、水印或剧情外物件
- 人物脸、手、眼睛、发型、衣服结构变形
- 背景透视断裂、边缘接缝明显、重复纹理明显
- UI alpha 丢失、边缘出现黑边 / 白边 / 锯齿
- 缩略图裁掉关键人物、脸部、道具或画面中心
- QA 记录缺少工具、参数、输出路径或结论

## 新增文档

- `docs/modules/image-remaster/README.md`
- `docs/modules/image-remaster/asset-inventory.md`
- `docs/modules/image-remaster/processing-standard.md`
- `docs/modules/image-remaster/sample-list.md`
- `docs/modules/image-remaster/output-naming-standard.md`
- `docs/worklogs/2026-05-29-image-remaster.md`

