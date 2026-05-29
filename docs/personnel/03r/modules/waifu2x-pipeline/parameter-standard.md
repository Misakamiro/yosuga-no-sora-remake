# waifu2x 参数标准

## 通用原则

waifu2x 只负责放大、降噪和细节清理，不负责改变构图、补画剧情内容或替换素材语义。所有参数必须写入 QA 记录，最终图必须能从文件名和状态表追踪回源文件。

默认工具：

- `waifu2x-caffe`
- `waifu2x-ncnn-vulkan`
- 其他等价 anime 插画模型工具

默认输出格式：`PNG`

## 标准参数组

### P01 背景 / CG 默认组

适用：`bg/*`、`content-data/bg/*`、确认后的 CG / event 图。

| 参数 | 值 |
| --- | --- |
| model | `anime_style_art_rgb` 或等价 anime 插画模型 |
| noise | `1` |
| scale | `2` |
| tta | 关闭；最终样张复核可开启 |
| format | PNG |
| 后处理 | 缩放或裁切回批准尺寸，背景 / CG 为 `1920x1080` |

使用条件：源图有可见压缩噪点、旧游戏低分辨率纹理、线条需要高清化。

### P02 背景低噪组

适用：经过 NovelAI 和手工修补后已经较干净的背景 / CG。

| 参数 | 值 |
| --- | --- |
| model | `anime_style_art_rgb` |
| noise | `0` |
| scale | `2` |
| tta | 关闭 |
| format | PNG |
| 后处理 | 与 P01 相同 |

使用条件：P01 出现过锐、皮肤塑料感、细节被抹平或纹理异常强化。

### P03 立绘 / 人物层组

适用：`content-data/char/*` 中确认是人物层、表情差分、服装差分的资源。

| 参数 | 值 |
| --- | --- |
| model | `anime_style_art_rgb` |
| noise | `1`，干净图可用 `0` |
| scale | `2` |
| tta | 默认关闭；最终主立绘样张可开启 |
| alpha | 必须保留 |
| 后处理 | 回写到批准裁切框，不改变站位和透明边界 |

QA 重点：脸、眼睛、手、发丝、衣领、透明边缘、脚底基准线。

### P04 UI / frame 组

适用：`frame/*` 中窗口、按钮、雪花、遮罩、特效碎片等 UI 资源。

| 参数 | 值 |
| --- | --- |
| model | `anime_style_art_rgb` |
| noise | `0-1` |
| scale | `2`，必要时只做降噪不放大 |
| tta | 关闭 |
| alpha | 必须保留 |
| 后处理 | 保持 UI 批准尺寸，不默认 16:9 化 |

QA 重点：透明边缘黑白边、按钮文字伪影、边框锐度、九宫格拉伸区域、动画帧一致性。

### P05 rule 转场组

适用：`rule/*` 转场规则 PNG。

| 参数 | 值 |
| --- | --- |
| model | `anime_style_art_rgb` |
| noise | `0` |
| scale | `1` 或 `2` |
| tta | 关闭 |
| format | PNG |
| 后处理 | 默认保持原尺寸；若放大，必须验证引擎采样行为 |

注意：转场规则图可能不是普通视觉素材，而是灰度 / 色阶驱动的 wipe mask。不能只凭肉眼好看就改变尺寸或灰阶关系。

### P06 thumb 缩略图组

适用：`content-data/thumb/*`。

默认不直接 waifu2x。缩略图应从 QA 通过的背景 / CG final 图重新生成，避免旧缩略图放大后变糊、变锐或裁切错误。

推荐候选尺寸：

- 保留原系统尺寸：`100x75`、`160x125`、`160x124`、`194x189`
- 16:9 预览候选：`320x180`、`480x270`

最终采用哪组尺寸，需要 UI / 引擎调用确认后再批量生成。

## NovelAI 与 waifu2x 的顺序

背景和 CG 推荐顺序：

1. 源图解码。
2. NovelAI outpaint 到目标构图。
3. 手工修补扩图接缝和错位细节。
4. waifu2x 放大 / 降噪。
5. 尺寸归一到 final。
6. QA。

不推荐先 waifu2x 再 NovelAI，因为放大后的纹理可能诱导 NovelAI 生成错误细节。

## QA 标准

通过条件：

- 源路径、输出路径、参数、工具版本、版本号齐全。
- 背景 / CG final 为 `1920x1080`，除非另有批准记录。
- UI / frame 保留透明通道，无黑边、白边、硬边。
- rule 图没有破坏原本灰阶 / 色阶 / alpha 语义。
- 缩略图没有裁掉人脸、标题、关键道具或画面中心。
- 未覆盖、移动或删除任何原始资源。

返工条件：

- 尺寸不符或缺少尺寸记录。
- waifu2x 输出过锐、过糊、颜色漂移或纹理重复。
- 人物脸、眼睛、手、发丝、衣服结构变形。
- NovelAI 生成额外人物、文字、水印、标志或剧情外物件。
- alpha 丢失、边缘锯齿、半透明阴影异常。
- 状态表缺少源路径、输出路径、参数或 QA 结论。

## 推荐命令记录格式

每次实际批处理必须记录实际工具和版本。示例字段：

```text
tool=waifu2x-ncnn-vulkan
version=<实际版本>
model=anime_style_art_rgb
noise=1
scale=2
tta=false
input=<stage input path>
output=<stage output path>
source=<original source path>
sha256=<source sha256>
```
