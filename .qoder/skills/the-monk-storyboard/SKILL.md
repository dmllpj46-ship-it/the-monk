---
name: the-monk-storyboard
description: 为本仓库（the-monk 日式漫画）创作一页新分镜并出图的完整流程：编号与目录、meta.md 分镜写法、生图提示词模板、PIL 叠字定稿、一致性校验。当用户说「画 00X」「新增一页」「写分镜」「生成页面」时使用。
---

# The Monk 分镜创作（仓库专用）

一页漫画 = 一个目录 + 一次生图 + 一次叠字。按下面五步顺序做，**不要跳步，也不要反复重生成**。

## 0. 先读三样东西

- `AGENTS.md` 的硬规则与 schema
- `characters/001-huiming/character.md` 的一致性锚点
- 上一页 `pages/NNN-*/meta.md`（承接叙事，别自相矛盾）

## 1. 建目录与 meta.md（先写字，后画图）

新页号 = 现有最大页号 +1，永不复用/跳号。`cp pages/_template/meta.md pages/NNN-slug/meta.md`。

分镜写法约定：

- **5 格纵向**是默认版式（P1…P5），封面除外；P3 通常是主格（最大）。
- 每格只写「景别 + 一个动作」：`P2 中景：…`。不要写两段动作挤一格。
- 文字与画面**分开写**：`对白（PIL 叠字，生图阶段画面零文字）` 单独一节。
- 需要交代时空时，用**时空框**（如「民国二十六年九月 / 晋北 · 正太路以北」），放在 P1，不要指望生图画出来。
- 涉及战斗/历史题材，meta.md 里写一行**表现尺度**：不画残肢血浆、敌兵不给面部特写。
- 状态先填「待生成」。

## 2. 生图：一次调用，画面零文字

用 ImageGen，`size=1024x1536`（2:3）。提示词必须包含这四块，缺一不可：

1. **风格头**：`Vertical Japanese manga page, 5 stacked panels, black-and-white ink line art with screentone halftone, heavy speed lines, thin black panel borders, white gutters`
2. **零文字铁律**：`ABSOLUTE RULE: every panel interior must be COMPLETELY FREE of letters, words, numbers, speech balloons, sound-effect typography, logos and watermarks — empty white caption boxes only.`（AI 直出中文必乱码，见 README 工具链）
3. **主角锚点**（从 `character.md` 逐条抄进提示词）：光头、眉心一点朱砂（全图唯一彩色）、**旧疤穿过画面左侧那条眉毛**、颌下短髭、灰僧袍、袈裟缠腰、草鞋、腕上佛珠、**双手空空——no staff / no weapon / no bundle**。
   - 方位坑：模型按**画面左右**理解，写 `viewer's LEFT`，不要写「左眉」。
   - 一致性靠把 `characters/001-huiming/avatar.png` 作为参考图传入（`images=[...]`）。
4. **逐格描述**：P1…P5 各一段，与 meta.md 一一对应；历史题材要写清年代服制（如 1937 日军：钢盔 / 带布帘的军帽 / 上刺刀的三八式步枪），并显式排除 `no flags, no insignia, no gore, no corpses`。

**出图后只做一次校验**（见第 4 步），合格就往下走。轻微偏差用叠字与文案规避，**不要**为了一个细节反复整页重生成——费 token 且越重生越漂。

## 3. 叠字：PIL 脚本，不入库

脚本命名 `_make_page_NNN.py`，放在仓库根（`.gitignore` 已忽略 `/_*`）。直接抄 `_make_page_002.py` 的骨架，只改常量：

- `SRC` 指向 `vibe_images/` 里的生成原图；先 `resize((1696, 2544), LANCZOS)` 统一到全仓库页宽，再另存 `page-NNN-clean.png`。
- `TEXTS` 每项：`kind`（`box` 时空框 / `narr` 旁白 / `mono` 内心 / `sfx` 拟声）、`text`、`x`、`y`（**百分比**，文字块左上角）、`size`、`halo`。
- 字体：旁白与独白用楷体 `C:/Windows/Fonts/simkai.ttf`；拟声用粗黑 `C:/Windows/Fonts/msyhbd.ttc`。
- 所有字都要带白描边（`halo`），否则压在网点上读不出；深色格上改用 `box`（白底圆角）或把字挪到留白区。
- 拟声给 `rot=-4 ~ -8` 才有冲击感；竖排内心独白用 `vertical=True`。
- **坐标换算**：1% 高度 ≈ 25px。竖排 N 字 ≈ `N × size × 1.32` px，别让独白串到下一格。

改文案只改 `TEXTS` 重跑，**对着 `page-NNN-clean.png` 重跑，不要回去重新生图**。

## 4. 一致性校验（一次，别循环）

对照 `character.md` 锚点逐项打勾写进 meta.md：光头 / 眉心朱砂 / 画面左侧眉疤 / 短髭 / 灰僧袍 / 佛珠 / 双手空空 / 黑白网点。

- 发现**主角手里有兵器**这类硬伤：优先「改文案 + 换格」规避；确需修图，用第 2 步的提示词**整页重生成一次**，不要做局部 clone/中值修补（深色僧袍上会留亮块，试过，不成立）。
- 需要看清细节时：PIL 按百分比裁格 + `resize(..., LANCZOS)` 存到 `vibe_images/_chk_*.png` 再 Read；定位坐标时先画红蓝网格再读，别凭感觉估。

## 5. 收尾

meta.md：填「产出」两行（`page-NNN.png` 定稿 / `page-NNN-clean.png` 无字底图 + 尺寸），状态改「定稿」。
README.md：进度表加一行。
提交：`feat: 生成页面 NNN-slug（一句话画面要点）`，push 走 HTTPS。
