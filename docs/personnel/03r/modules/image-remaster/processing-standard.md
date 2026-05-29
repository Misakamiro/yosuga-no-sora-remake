# 处理流程标准

## 总流程

固定流水线：

1. 原图备份
2. TLG / PNG 解码为工作图
3. NovelAI 16:9 扩图
4. Photoshop 修图
5. waifu2x 放大 / 去噪
6. 人工 QA
7. 最终输出

所有阶段文件都写入：

`C:\Users\misakamiro\Downloads\Compressed\yosuga_krkrZ\yosuga_krkrZ\remaster-output\image-remaster`

禁止覆盖 `bg`、`content-data`、`frame`、`rule`、`thumb` 下的原始文件。

## 1. 原图备份

工具：

- PowerShell `Copy-Item`
- `Get-FileHash`

参数和要求：

- 按原始相对路径复制到 `00-original-backup`
- 备份后记录 SHA256
- 同名文件不得覆盖，二次备份加 `__v002`
- 0 字节占位图只登记，不进入高清化流程

备份输出示例：

`remaster-output/image-remaster/00-original-backup/bg/B01a.tlg`

## 2. 解码为工作图

工具：

- GARbro / KrkrExtract / 可读取 TLG5/TLG6 的 TLG 转换器
- ImageMagick 或同类工具用于尺寸复核

参数和要求：

- `.tlg` 解码为无损 PNG 或 TIFF
- PNG 保留 alpha
- 解码后检查宽高、透明通道、边缘是否断裂
- 解码失败、透明丢失、颜色偏移必须返工

解码输出示例：

`remaster-output/image-remaster/01-decoded/bg/B01a__decoded.png`

## 3. NovelAI 16:9 扩图

工具：

- NovelAI 图生图 / 扩图（outpainting）

通用参数：

- 目标画布：`1920x1080`
- 背景 / CG 起始策略：先扩左右，再微调上下，不直接拉伸主体
- 原图保留强度：`0.50-0.65`
- 去噪 / strength 建议：`0.35-0.50`
- 随机种子（seed）：固定并记录
- 正向提示词（prompt）：描述场景、光照、透视、原作风格；不得加入新角色或改变剧情信息
- 负向提示词（negative prompt）：`extra limbs, extra people, changed character design, text artifact, logo, watermark, low quality, blurry, distorted perspective`

背景参数：

- `800x600` 背景扩为 16:9 时，以中心主体为基准左右补画
- 若原图是 `800x1200` 或 `800x1201`，先判定是否为卷动背景；不得直接裁掉关键区域
- 宽背景如 `1766x600` 先按构图缩放到目标宽度，再补上下空间

CG / event 参数：

- 人物脸、手、衣纹、关键物件不允许变形
- 对话相关画面不得生成额外人物、文字、徽标
- 若存在多图层组合，先确认合成结果，再处理最终可见画面

立绘 / 人物层参数：

- 不默认进入全屏 16:9 扩图
- 优先保持 alpha、裁切框、立绘脚底 / 头顶基准线
- 若需要高清化，按单体放大和边缘修复处理，不改变站位和表情

输出示例：

`remaster-output/image-remaster/02-nai-outpaint/bg/B01a__outpaint16x9__seed123456__v001.png`

## 4. Photoshop 修图

工具：

- Adobe Photoshop

处理项：

- 修补 NovelAI 扩图边缘接缝
- 修正透视、直线、建筑边缘、室内结构
- 修正人物脸、手、发丝、眼睛、衣领、饰品
- 去除多余纹理、文字伪影、水印状噪点
- 对横向宽背景和竖向卷动背景做构图安全检查

参数和要求：

- 保存 PSD 工作文件
- 图层命名清楚：`base`、`nai_outpaint`、`paint_fix`、`color_match`、`qa_notes`
- 输出一份平面 PNG 给 waifu2x

输出示例：

`remaster-output/image-remaster/03-ps-fix/bg/B01a__psfix__v001.psd`

## 5. waifu2x 放大 / 去噪

工具：

- waifu2x-caffe / waifu2x-ncnn-vulkan / 等价 anime 模型

建议参数：

- 模型（Model）：anime_style_art_rgb 或同等二次元插画模型
- 去噪等级（Noise reduction）：`1`
- 放大倍率（Scale）：`2x`
- TTA：批量处理默认关闭；最终样张可开启复核
- 输出格式：PNG

处理规则：

- 如果 waifu2x 输出超过目标尺寸，最后一步用高质量缩放回目标尺寸
- 背景 / CG 最终必须为 `1920x1080`
- UI 和缩略图按对应需求输出，不套用背景尺寸

输出示例：

`remaster-output/image-remaster/04-waifu2x/bg/B01a__waifu2x__noise1_scale2__v001.png`

## 6. 人工 QA

检查视图：

- 100% 查看整体构图
- 200% 查看脸、手、文字、边缘和扩图接缝
- 与原图并排比较主体比例、光照方向、颜色倾向

必须记录：

- 源路径
- 输出路径
- 工具版本
- NovelAI 正向提示词、负向提示词、随机种子、strength
- PS 修图说明
- waifu2x 参数
- QA 结论
- 是否返工

QA 记录输出：

`remaster-output/image-remaster/06-qa/YYYY-MM-DD/<asset-stem>__qa.md`

## 最终输出标准

背景 / CG：

- 尺寸：`1920x1080`
- 色彩：与原图接近，不明显漂白、过饱和或偏灰
- 格式：PNG
- 命名：见 `output-naming-standard.md`

UI 图：

- 按 UI 组确定尺寸
- 小组件不得强行 16:9 化
- 透明通道必须保留

缩略图：

- 从最终 CG / 背景重新生成
- 按系统窗口尺寸输出
- 未确认窗口规格前，可先准备 `320x180` 或 `480x270` 16:9 候选，同时保留原始缩略图尺寸记录

转场图：

- 如果作为全屏遮罩使用，可做 `1920x1080`
- 如果作为规则贴图被脚本按 `800x600` 采样，必须保持原尺寸或等 UI / 引擎组确认后再改

## 返工标准

出现以下任一情况必须返工：

- 输出不是目标尺寸，或尺寸记录缺失
- 覆盖、移动、删除了原始资源
- 人物脸、手、眼睛、发型、衣服结构明显变形
- 背景透视错误，门窗、桌椅、墙线明显扭曲
- NovelAI 生成了新人物、新文字、水印、标志或剧情外物件
- 扩图边缘有接缝、重复纹理、糊块、噪点带
- 颜色与原图差异过大，影响昼夜、季节或剧情氛围
- 透明 UI 边缘出现黑边、白边、锯齿或 alpha 丢失
- 缩略图裁掉人物脸、关键道具或构图中心
- QA 记录缺少工具、参数、输出路径或结论
