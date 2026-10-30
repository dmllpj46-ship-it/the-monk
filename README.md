# The Monk — 和尚的冒险

> 用 AI 图像生成制作的**日式漫画**：一个拥有**法力**与**战力**的和尚的冒险故事。
>
> 在线阅读：<https://the-monk.pages.dev>（Cloudflare Pages，页面图直连本仓库 raw 链接）

## 项目定位

- 目标产物：日式漫画（默认黑白线稿风，可按需切换彩色）。
- 生产方式：AI 图像生成（QwenWork 图像管线，实际模型 `banana-image-2`）逐页生成，人工/智能体协同编辑；页面为**纯图片**，画面上不出现任何文字。
- 代码托管：GitHub 公开仓库（账号 `dmllpj46-ship-it`，HTTPS 推送）。

## 故事设定

主角是一位云游僧人，身负秘传法力，一双肉掌同样凌厉。他行走于妖邪横行的世道，
以法净化、以掌开路——但真正要渡的，或许是他自己背负的因果。

> 详细角色与剧情见 `characters/`、`pages/` 下的元数据；本 README 只维护概要与进度。

## 目录结构

```
the-monk/
├── README.md            # 本文件：项目概览 + 进度
├── AGENTS.md            # 给 AI 智能体的工作守则
├── .qoder/skills/       # 仓库专用技能（分镜创作流程 the-monk-storyboard）
├── site/                # 在线阅读站点（单文件静态页，部署到 Cloudflare Pages）
├── characters/          # 角色：每人一个编号子目录
│   ├── _template/       # character.md 模板
│   └── NNN-slug/
│       ├── avatar.png   # 角色头像（一致性参考图）
│       └── character.md # 角色元数据
└── pages/               # 漫画页：每页一个编号子目录
    ├── _template/       # meta.md 模板
    └── NNN-slug/
        ├── page-NNN.png        # 页面图像（纯图，画面上不出现任何文字）
        └── meta.md             # 页面元数据（场景/分镜/出场/状态）
```

## 命名与编号规则

| 对象 | 规则 | 示例 |
|---|---|---|
| 角色 | `characters/NNN-slug/`，NNN 从 001 起递增，不复用 | `characters/001-huiming/` |
| 角色头像 | 固定名 `avatar.png` | `characters/001-huiming/avatar.png` |
| 漫画页 | `pages/NNN-slug/`，NNN 从 001 起递增，不复用 | `pages/002-mountain-clash/` |
| 页面图像 | `page-NNN.png`（与目录号一致） | `pages/002-mountain-clash/page-002.png` |
| 元数据 | 角色 `character.md`，页面 `meta.md`，模板见 `_template/` | — |

## 元数据字段

- `character.md`：角色编号、法号/姓名、身份、外貌、服装、法力、战力、性格、口头禅、配色、生成提示词锚点、状态。
- `meta.md`：页号、类型（封面/分镜）、场景、时间地点、出场角色、动作分镜、叙事节点、状态（待生成 / 已生成 / 待审 / 定稿）。

详细 schema 见 `AGENTS.md` 与各 `_template/`。

## 制作工作流

1. 定角色 → 建 `characters/NNN-slug/`，写 `character.md`，生成 `avatar.png`（此后所有画面引用它保证一致性）。
2. 写分镜 → 建 `pages/NNN-slug/meta.md`，写明场景、出场角色、镜头、叙事节点。
3. 生成页面 → 按 `meta.md` 与角色设定一次出图，Lanczos 放大到 1696×2544 存为 `page-NNN.png`（纯图，不叠字）。
4. 校验一致性 → 对照 `character.md` 核对外貌/服装/能力。
5. 更新进度 → 回填 README 进度表，提交推送。

> 以上五步的细则（分镜写法、生图提示词四段模板、纯图定稿、一次性一致性校验）见仓库专用技能
> [`.qoder/skills/the-monk-storyboard/SKILL.md`](file:///C:/Users/WaterZhong/REPOS-LOCAL/the-monk/.qoder/skills/the-monk-storyboard/SKILL.md)。

## 当前进度

| 项 | 状态 |
|---|---|
| 角色：慧明（主角，占位名） | 设定已建，头像已定 |
| 页面：001 封面 | 定稿（THE MONK + 朱砂红「因果自渡。」） |
| 页面：002 石中僧 | 定稿（主角不设禅杖，本页空手即正确画法） |
| 页面：003 白骨下的经轮 | 定稿（1937·晋北，徒手接刃 + 金刚手印；P5 背影手里多一截断兵器，已知偏差） |
| 页面：004 草木禅 | 定稿（东非草原·旱季，虎豹狼虫为伴；纯图页） |
| 页面：005 隔岸观火 | 定稿（欧洲老城夜乱，火光与烟花下的冷眼旁观；纯图页） |
| 页面：006 满街颜色 | 定稿（纽约夜，彩虹游行与冷眼旁观；破例上色，唯一彩漫页） |
| 页面：007 广寒残宫 | 定稿（月面，环形山与广寒宫废墟；纯图页） |
| 页面：008 光环 | 定稿（黑洞前观音显圣，光环作背光；纯图页） |

## 工具链

- 图像生成：QwenWork 图像管线（实际出图模型 `banana-image-2`）。角色一致性靠把 `avatar.png` 作为参考图传入 `mode=edit`。
- 页面文字：**页面是纯图片，不叠任何文字**。提示词里显式排除 letters / speech balloons / SFX typography，连白框也不要。
- 仓库操作：`git` over HTTPS（账号 `dmllpj46-ship-it`）。
