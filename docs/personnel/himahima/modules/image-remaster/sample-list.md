# 流程验证样张

样张选择日期：2026-05-29

本轮只选择样张并规定处理路径；尚未执行 NovelAI、Photoshop、waifu2x 实际出图。

输出根目录：

`C:\Users\misakamiro\Downloads\Compressed\yosuga_krkrZ\yosuga_krkrZ\remaster-output\image-remaster`

## 样张列表

| 编号 | 类型 | 源路径 | 源尺寸 | 选择理由 | 最终目标 |
| --- | --- | --- | --- | --- | --- |
| S01 | 背景 | `bg/B01a.tlg` | `800x600` | 标准 4:3 背景，适合验证左右 16:9 扩图 | `1920x1080` |
| S02 | CG / event 候选 | `content-data/char/SP01.tlg` | `800x600` | `SP` 前缀全屏事件图候选，适合验证 CG 扩图 | `1920x1080` |
| S03 | 立绘 / 人物层候选 | `content-data/char/cc01_01l.tlg` | `714x738` | 竖向人物层尺寸，适合验证 alpha、边缘和人物比例保护 | 按调用方式确认；不默认 16:9 |
| S04 | UI 图 | `frame/FRM_02A02.tlg` | `640x568` | 较大 UI frame，适合验证 UI 不强制拉伸和透明边缘 QA | 按 UI 组需求 |
| S05 | 缩略图 | `content-data/thumb/ca01_01TS.tlg` | `100x75` | 常见缩略图尺寸，适合验证由最终图重新生成缩略图 | 按系统窗口需求 |

转场图不进入本轮 5 张主样张，但已登记代表文件：

- `rule/CLOUD_A.png`，`800x600`
- `rule/curtain_close.png`，`800x600`
- `rule/shutter_open.png`，`800x600`

如果后续需要把转场也纳入样张，优先把 S05 缩略图改为 `rule/CLOUD_A.png`，缩略图改为由 S02 的最终图派生验证。

## 样张处理参数

### S01 背景：`bg/B01a.tlg`

使用工具：

- TLG 解码器：输出 PNG
- NovelAI：16:9 扩图（outpainting）
- Photoshop：修补接缝、透视、色彩
- waifu2x：二次元模型去噪放大

参数：

- NovelAI 目标画布：`1920x1080`
- NovelAI 原图保留强度：`0.50-0.65`
- NovelAI 去噪 / strength：`0.35-0.45`
- NovelAI 随机种子：固定记录
- waifu2x 去噪等级：`1`
- waifu2x 放大倍率：`2x`

输出路径：

`remaster-output/image-remaster/05-final/bg/B01a__bg__1920x1080__v001.png`

返工标准：

- 左右扩图出现重复纹理、透视断裂或主体被改动
- 最终尺寸不是 `1920x1080`
- 颜色、光照和原图明显不一致

### S02 CG / event 候选：`content-data/char/SP01.tlg`

使用工具：

- TLG 解码器
- NovelAI 扩图（outpainting）
- Photoshop
- waifu2x

参数：

- NovelAI 目标画布：`1920x1080`
- NovelAI 原图保留强度：`0.55-0.70`
- NovelAI 去噪 / strength：`0.30-0.45`
- 负向提示词必须包含：`extra people, changed character design, text artifact, watermark`
- waifu2x 去噪等级：`1`
- waifu2x 放大倍率：`2x`

输出路径：

`remaster-output/image-remaster/05-final/cg/SP01__cg__1920x1080__v001.png`

返工标准：

- 人物、关键物件或画面主体被改写
- 生成额外人物、文字、水印或剧情外物件
- 构图中心偏移导致原始叙事重点丢失

### S03 立绘 / 人物层候选：`content-data/char/cc01_01l.tlg`

使用工具：

- TLG 解码器
- Photoshop
- waifu2x
- NovelAI 仅在需要补边时使用，不默认全屏扩图

参数：

- 透明通道：必须保留
- 人物层默认不改站位、不改裁切框语义
- waifu2x 去噪等级：`1`
- waifu2x 放大倍率：`2x`
- 如果需要扩画布，先输出独立人物层，再由 UI / 演出组确认摆放

输出路径：

`remaster-output/image-remaster/05-final/char/cc01_01l__char-layer__native-or-approved__v001.png`

返工标准：

- alpha 丢失
- 头顶、脚底、发丝、衣边被截断
- 人物比例、脸型、表情、站姿改变
- 输出尺寸没有记录来源和审批原因

### S04 UI 图：`frame/FRM_02A02.tlg`

使用工具：

- TLG 解码器
- Photoshop
- waifu2x 或矢量 / 重绘替代方案

参数：

- 不默认 16:9 化
- 先保留原始比例和透明边界
- UI 组确认后再决定 1x、2x、3x 或目标像素尺寸
- waifu2x 去噪等级：`0-1`
- waifu2x 放大倍率：`2x`，仅用于边缘质量验证

输出路径：

`remaster-output/image-remaster/05-final/ui/FRM_02A02__ui__approved-size__v001.png`

返工标准：

- UI 边缘出现黑边、白边、锯齿
- alpha 丢失
- 拉伸导致圆角、线框、按钮比例异常
- 未记录 UI 组确认尺寸

### S05 缩略图：`content-data/thumb/ca01_01TS.tlg`

使用工具：

- TLG 解码器
- Photoshop 或 ImageMagick

参数：

- 由对应最终 CG / 背景重新生成，不直接把 `100x75` 放大成最终缩略图
- 未确认系统窗口前，候选输出为 `320x180` 和 `480x270`
- 保留原始 `100x75` 兼容记录

输出路径：

`remaster-output/image-remaster/05-final/thumb/ca01_01TS__thumb__320x180__v001.png`

返工标准：

- 裁掉人物脸、关键道具或画面中心
- 缩略图模糊、锯齿明显
- 与对应最终 CG / 背景不一致
- 未记录系统窗口需求或候选尺寸理由

## 样张 QA 交付

每张样张必须额外交付 QA 记录：

`remaster-output/image-remaster/06-qa/2026-05-29/<asset-stem>__qa.md`

QA 记录必须包含：

- 样张源路径
- 每个阶段输出路径
- NovelAI 正向提示词、负向提示词、随机种子、strength
- Photoshop 修图说明
- waifu2x 参数
- 人工检查结论
- 是否返工，以及返工原因
