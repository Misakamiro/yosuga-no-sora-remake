# 输出目录与命名标准

## 输出根目录

所有处理产物必须写入：

`C:\Users\misakamiro\Downloads\Compressed\yosuga_krkrZ\yosuga_krkrZ\remaster-output\image-remaster`

不得写回原目录：

- `bg`
- `content-data`
- `frame`
- `rule`
- `thumb`
- `system`
- `scenario`

## 阶段目录

| 阶段 | 目录 | 内容 |
| --- | --- | --- |
| 00 | `00-original-backup` | 原始资源备份，保持相对路径 |
| 01 | `01-decoded` | TLG / PNG 解码后的工作 PNG / TIFF |
| 02 | `02-nai-outpaint` | NovelAI 16:9 扩图中间结果 |
| 03 | `03-ps-fix` | Photoshop 工作文件和修图平面图 |
| 04 | `04-waifu2x` | waifu2x 放大 / 去噪结果 |
| 05 | `05-final` | 最终交付图 |
| 06 | `06-qa` | QA 记录、截图、返工说明 |
| 99 | `99-rejected` | 已判定不合格但需要留档的旧版本 |

## 基本命名格式

最终输出：

`<source-stem>__<asset-type>__<size-or-approved-size>__v<version>.<ext>`

示例：

- `B01a__bg__1920x1080__v001.png`
- `SP01__cg__1920x1080__v001.png`
- `cc01_01l__char-layer__native-or-approved__v001.png`
- `FRM_02A02__ui__approved-size__v001.png`
- `ca01_01TS__thumb__320x180__v001.png`
- `CLOUD_A__rule__1920x1080__v001.png`

## 阶段命名格式

### 原始备份

保留原始相对路径：

`00-original-backup/<original-relative-path>`

示例：

`00-original-backup/bg/B01a.tlg`

### 解码图

`<source-stem>__decoded.<ext>`

示例：

`01-decoded/bg/B01a__decoded.png`

### NovelAI 扩图

`<source-stem>__outpaint16x9__seed<seed>__v<version>.png`

示例：

`02-nai-outpaint/bg/B01a__outpaint16x9__seed123456__v001.png`

### Photoshop 修图

工作文件：

`<source-stem>__psfix__v<version>.psd`

平面输出：

`<source-stem>__psfix-flat__v<version>.png`

示例：

`03-ps-fix/bg/B01a__psfix__v001.psd`

### waifu2x

`<source-stem>__waifu2x__noise<noise>_scale<scale>__v<version>.png`

示例：

`04-waifu2x/bg/B01a__waifu2x__noise1_scale2__v001.png`

### QA

`<source-stem>__qa.md`

示例：

`06-qa/2026-05-29/B01a__qa.md`

## 资源类型标识

| 标识 | 用途 |
| --- | --- |
| `bg` | 背景 |
| `cg` | CG / event 全屏图 |
| `char-layer` | 立绘 / 人物层 |
| `ui` | UI 图 |
| `thumb` | 缩略图 |
| `rule` | 转场图 / 规则图 |

## 版本规则

- 首次输出使用 `v001`
- 每次返工递增版本号，例如 `v002`
- 不允许覆盖旧版本
- 被淘汰但需要留档的版本移动到 `99-rejected`
- QA 记录中必须说明当前最终版本和被拒版本原因

## 路径示例

背景最终输出：

`remaster-output/image-remaster/05-final/bg/B01a__bg__1920x1080__v001.png`

CG 最终输出：

`remaster-output/image-remaster/05-final/cg/SP01__cg__1920x1080__v001.png`

人物层最终输出：

`remaster-output/image-remaster/05-final/char/cc01_01l__char-layer__native-or-approved__v001.png`

UI 最终输出：

`remaster-output/image-remaster/05-final/ui/FRM_02A02__ui__approved-size__v001.png`

缩略图最终输出：

`remaster-output/image-remaster/05-final/thumb/ca01_01TS__thumb__320x180__v001.png`

