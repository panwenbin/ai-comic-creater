# 领域模型（Domain Model）

> 状态：Draft
>
> 目的：定义项目的**业务语言、核心概念、关系、规则和不变量**。除非某项技术本身就是领域概念，否则不要在这里引入框架、数据库或具体厂商。

## 1. 领域概述

本项目的业务领域是：

> **将已有故事逐步转换为结构化、可控制、可复用的 AI 漫剧视频素材。**

一个漫剧项目由多个章节以及可跨章节复用的资产组成。正式生产以已确定的剧本为基准；用户可以直接提供标准剧本，也可以先将故事转换为剧本。

每个章节以已确定的 Script 为正式生产输入。从 Script 识别资产需求，优先匹配复用项目中已有资产，确认新增或重要变化的资产后，再生成 Storyboard。

Storyboard 由按照剧情顺序排列的 Shot 组成。生成 Storyboard 时，同时生成完整的 Shot List 以及每个 Shot 的文字定义，因此不存在独立的“Storyboard 生成后再拆 Shot”阶段。

Shot 是后续视频生成的基本单位，可以直接生成视频片段，也可以选择先生成静态预览图，再用于视频生成。

角色、场景和道具是项目级可复用资产。正式资产需求从已确定的 Script 中识别，并与项目已有资产匹配；新增或发生重要变化的资产经用户确认后，可以被当前章节及后续章节持续引用。

核心关系可以概括为：

```text
Project
├── Character *
├── Scene *
├── Prop *
│
└── Chapter *
    ├── Story（可选）
    ├── Script
    └── Storyboard
        └── Shot *
            ├── Preview *       (可选)
            └── Video Clip *
```

Shot 同时引用项目中的 Character、Scene 和 Prop。

---

## 2. 统一语言（Ubiquitous Language）

| 术语 | 定义 | 备注 / 避免使用的同义词 |
| --- | --- | --- |
| Project（项目） | 一个完整的 AI 漫剧制作项目，包含多个章节和可跨章节复用的资产 | 当前不额外区分 Work / Series |
| Chapter（章节） | 项目中的一个故事章节，也是主要制作范围单位 | 对应原始故事中的章节 |
| Story（故事） | 用户提供的原始故事内容 | 不等同于 Script |
| Script（剧本） | Story 经过漫剧化处理后形成的正式制作内容 | 是正式阶段，不是临时中间文本 |
| Storyboard（分镜） | 根据 Script 生成的完整镜头组织方案，由有序 Shot List 构成 | 不把 Storyboard 和 Shot List 视为两个生成阶段 |
| Shot（镜头） | Storyboard 中的单个镜头，也是视频生成的基本单位 | 不使用 Scene 表示镜头，避免与场景混淆 |
| Character（角色） | 剧本中需要保持身份和视觉一致性的角色 | 项目级资产，可跨章节复用 |
| Scene（场景） | 剧本中可重复出现并需要保持视觉一致性的地点或环境 | 不表示剧本场次或镜头 |
| Scene Variant（场景变体） | 同一 Scene 在不同时间、天气、季节等条件下的视觉状态 | 是否作为正式概念仍需验证 |
| Prop（道具） | 具有剧情意义或需要保持视觉一致性的物品 | 普通背景物品不一定成为 Prop |
| Asset（资产） | 可以被后续制作持续引用和复用的稳定内容 | 当前核心资产为 Character / Scene / Prop |
| Visual Asset（视觉资产） | Character、Scene、Prop 等资产对应的视觉表现 | 如角色概念图、Character Sheet、场景参考图 |
| Preview（预览图） | 根据 Shot 生成的可选静态画面 | 不是视频生成的强制前置步骤 |
| Video Clip（视频片段） | 根据单个 Shot 生成的视频结果 | 一个 Shot 可以拥有多个候选结果 |
| Confirmed（已确认） | 用户认可关键资产可以作为后续制作依据的状态 | 主要用于 Character / Scene / Prop |
| Affected（可能受影响） | 上游内容修改后，下游已有结果可能与最新内容不一致的状态 | 不等同于自动失效 |

---

## 3. 核心概念

### Project

**含义**  
一个完整的 AI 漫剧制作项目，是章节和跨章节资产的组织边界。

**身份与生命周期**  
Project 具有独立身份。

创建 Project 后，可以持续增加 Chapter，并逐步建立和复用 Character、Scene、Prop 等资产。

Project 可以持续存在直到整个漫剧制作结束。

**关键属性**

- 项目名称；
- 项目描述；
- Chapter 集合；
- Character 集合；
- Scene 集合；
- Prop 集合。

**规则 / 不变量**

- 一个 Chapter 必须属于一个 Project；
- Character、Scene、Prop 在 Project 范围内复用；
- 同一业务对象跨章节出现时，应优先复用已有资产，而不是重复创建。

**关系**

- 包含多个 Chapter；
- 包含多个 Character；
- 包含多个 Scene；
- 包含多个 Prop。

---

### Chapter

**含义**  
Project 中的一个故事章节，是当前主要的内容制作范围单位。

**身份与生命周期**  
Chapter 具有独立身份。

Chapter 从原始 Story 开始，逐步产生 Script、Storyboard 和最终视频素材。

**关键属性**

- 章节名称；
- 章节顺序；
- Story；
- Script；
- Storyboard。

**规则 / 不变量**

- Chapter 必须属于一个 Project；
- Chapter 的原始 Story 不应被后续生成结果覆盖；
- Chapter 可以引用 Project 中已经确认的资产。

**关系**

- 属于一个 Project；
- 拥有 Story；
- 拥有 Script；
- 拥有 Storyboard；
- 引用 Project 中的 Character、Scene、Prop。

---

### Story

**含义**  
用户可选提供的原始故事内容，可用于转换为剧本，但不是正式生产的必备输入。

Story 主要表达：

> **故事发生了什么。**

**身份与生命周期**  
Story 与 Chapter 关联。

Story 在后续制作过程中始终作为原始来源保留。

**关键属性**

- 原始故事文本；
- 内容版本。

**规则 / 不变量**

- Story 不等同于 Script；
- 生成 Script 不应覆盖 Story；
- Story 修改后，已有 Script 以及相关下游内容可能受到影响。

**关系**

- 属于 Chapter；
- 是 Script 的内容来源；
- 可用于前期内容理解，但正式资产需求应依据已确定的 Script。

---

### Script

**含义**  
用于漫剧制作的正式剧本，可由用户直接提供，也可由 Story 转换得到。

Script 主要表达：

> **故事应该如何被演出来。**

它负责将文学性的故事内容整理为适合视觉表现和后续 Storyboard 生成的内容。

**身份与生命周期**  
Script 是正式领域对象。

它可以由用户直接提供，或由 Story 转换得到；之后可以被修改或重新生成。

**关键属性**

- 剧本内容；
- 与 Story 的来源关系；
- 当前版本。

**规则 / 不变量**

- Script 可以直接由用户提供，也可以由 Story 转换；
- Script 是正式生产的标准输入；
- Storyboard 应基于 Script 生成；
- 修改 Script 后，已有 Storyboard 可能受到影响。

**关系**

- 属于 Chapter；
- 可选来源于 Story；
- 是资产需求识别及 Storyboard 的正式内容依据。

---

### Storyboard

**含义**  
根据 Script 生成的完整镜头组织方案。

Storyboard 主要表达：

> **这个剧本应该由哪些镜头组成，以及这些镜头按照什么顺序出现。**

**身份与生命周期**  
Storyboard 属于 Chapter。

生成 Storyboard 时，同时产生完整的 Shot List 以及每个 Shot 的文字定义。

**关键属性**

- Shot List；
- Shot 顺序；
- Storyboard 整体说明；
- 来源 Script。

**规则 / 不变量**

- Storyboard 必须来源于 Script；
- Storyboard 由一个或多个 Shot 组成；
- Storyboard 生成完成时，Shot List 同时存在；
- 不存在独立的“Storyboard → 再生成 Shot List”业务阶段；
- Shot 顺序决定最终视频素材的剧情顺序。

**关系**

- 属于 Chapter；
- 来源于 Script；
- 包含多个 Shot。

---

### Shot

**含义**  
Storyboard 中的单个镜头，是视频生成的基本业务单位。

Shot 主要表达：

> **这一段视频具体应该表现什么。**

**身份与生命周期**  
Shot 具有独立身份，但必须属于一个 Storyboard。

Shot 在 Storyboard 生成时创建，其文字定义同时产生。

之后可以修改，并生成 Preview 或 Video Clip。

**关键属性**

- Shot 顺序；
- 剧情内容；
- 画面描述；
- Character 引用；
- Scene 引用；
- Prop 引用；
- 人物动作；
- 人物表情和状态；
- 景别；
- 机位；
- 构图；
- 摄像机运动；
- 光线与氛围；
- 台词；
- 声音要求；
- 目标时长；
- 用户可调整的视频生成描述。

**规则 / 不变量**

- Shot 必须属于 Storyboard；
- Shot 必须具有足够完整的文字定义；
- Shot 是 Video Clip 的基本生成单位；
- Shot 可以直接生成 Video Clip；
- Preview 不是 Video Clip 的强制前置条件；
- Shot 引用 Character、Scene、Prop，而不是重新定义这些资产的稳定特征。

**关系**

- 属于 Storyboard；
- 引用 Character；
- 引用 Scene；
- 可以引用多个 Prop；
- 可以拥有多个 Preview；
- 可以拥有多个 Video Clip。

---

### Character

**含义**  
故事中需要保持身份和视觉一致性的角色。

**身份与生命周期**  
Character 具有独立身份，属于 Project。

Character 通常从某个 Chapter 的 Script 中首次识别，但建立后成为 Project 级资产，可以跨 Chapter 复用。

**关键属性**

- 名称；
- 年龄或年龄阶段；
- 性别；
- 身份；
- 外貌；
- 体型；
- 发型；
- 服装；
- 性格与气质；
- 稳定视觉特征；
- Visual Asset；
- 确认状态。

**规则 / 不变量**

- 同一个角色跨章节出现时，应优先复用已有 Character；
- Character 必须确认后才能作为正式资产进入后续制作；
- Shot 中的临时动作、表情等不应修改 Character 的稳定定义。

**关系**

- 属于 Project；
- 可以被多个 Chapter 使用；
- 可以被多个 Shot 引用；
- 可以拥有多个 Visual Asset。

---

### Scene

**含义**  
故事中可重复出现并需要保持视觉一致性的地点或环境。

**身份与生命周期**  
Scene 具有独立身份，属于 Project。

Scene 可以从某个 Chapter 的 Script 首次识别，之后跨 Chapter 复用。

**关键属性**

- 名称；
- 地点描述；
- 环境特征；
- 空间特征；
- 稳定视觉特征；
- Visual Asset；
- 确认状态。

**规则 / 不变量**

- 同一个地点跨章节出现时，应优先复用已有 Scene；
- Scene 必须确认后才能作为正式资产进入后续制作；
- 时间、天气、季节变化不应默认创建新的 Scene。

**关系**

- 属于 Project；
- 可以被多个 Shot 引用；
- 可以拥有多个 Visual Asset；
- 可以拥有 Scene Variant。

---

### Prop

**含义**  
剧本中具有剧情意义或需要保持视觉一致性的物品。

**身份与生命周期**  
Prop 具有独立身份，属于 Project。

Prop 可以从某个 Chapter 的 Script 首次识别，之后跨 Chapter 复用。

**关键属性**

- 名称；
- 类型；
- 外观；
- 关键特征；
- 剧情意义；
- Visual Asset；
- 确认状态。

**规则 / 不变量**

- 并非故事中的所有物品都必须成为 Prop；
- 只有需要保持一致性或具有剧情意义的物品才需要成为正式 Prop；
- Prop 必须确认后才能作为正式资产进入后续制作。

**关系**

- 属于 Project；
- 可以被多个 Shot 引用；
- 可以拥有多个 Visual Asset。

---

### Visual Asset

**含义**  
Character、Scene 或 Prop 已生成并用于后续视觉一致性参考的视觉表现。

例如：

- 角色概念图；
- Character Sheet；
- 角色细节图；
- 场景全景图；
- 场景状态参考图；
- 道具标准图。

**身份与生命周期**  
Visual Asset 具有独立生成结果。

同一个业务资产可以拥有多个 Visual Asset，并选择其中适合的结果作为当前参考。

**关键属性**

- 资产类型；
- 视觉内容；
- 所属业务对象；
- 当前是否被选用。

**规则 / 不变量**

- Visual Asset 不等同于 Character、Scene 或 Prop 本身；
- 删除或替换 Visual Asset 不应改变业务对象的身份；
- 同一个业务对象可以拥有多个视觉结果。

**关系**

- 属于 Character、Scene 或 Prop；
- 可以被 Shot 的生成过程引用。

---

### Preview

**含义**  
根据 Shot 生成的可选静态画面。

主要用于在视频生成前检查构图、人物位置和整体视觉表现。

**身份与生命周期**  
Preview 由 Shot 产生。

同一个 Shot 可以多次生成 Preview，并从中选择需要使用的结果。

**关键属性**

- 所属 Shot；
- 静态视觉结果；
- 是否被选用。

**规则 / 不变量**

- Preview 是可选内容；
- Shot 没有 Preview 也可以生成 Video Clip；
- Preview 不改变 Shot 本身的业务定义。

**关系**

- 属于 Shot；
- 可以作为 Video Clip 生成时的视觉参考。

---

### Video Clip

**含义**  
根据单个 Shot 生成的视频片段。

**身份与生命周期**  
Video Clip 由 Shot 产生。

同一个 Shot 可以存在多个 Video Clip 候选结果。

用户可以选择其中一个作为当前使用版本。

**关键属性**

- 所属 Shot；
- 视频结果；
- 是否被选用。

**规则 / 不变量**

- Video Clip 必须对应一个 Shot；
- 一个 Shot 可以存在多个候选 Video Clip；
- 重新生成不应强制覆盖已有候选结果。

**关系**

- 属于 Shot；
- 可以引用 Preview 作为生成参考；
- 最终按照 Shot 顺序组织为章节视频素材。

---

## 4. 概念关系

```text
Project
│
├── owns ───────────────> Character *
├── owns ───────────────> Scene *
├── owns ───────────────> Prop *
│
└── contains ───────────> Chapter *
                            │
                            ├── optionally has ─> Story
                            │                 │
                            │                 └── may produce ─> Script
                            │                                  │
                            │                                  └── produces ──> Storyboard
                            │                                                   │
                            │                                                   └── contains ──> Shot *
                            │
                            └─────────────────────────────────────────────────────────────┐
                                                                                          │
Character ─────────────────────────────────────────────── referenced by ──────────────────┤
Scene ─────────────────────────────────────────────────── referenced by ──────────────────┤
Prop ──────────────────────────────────────────────────── referenced by ──────────────────┘

Shot
├── optionally produces ──> Preview *
└── produces ─────────────> Video Clip *
```

核心关系含义：

- Project 是章节和跨章节资产的组织边界；
- Story 是可选的原始内容来源；
- Script 是正式生产的标准输入；
- Storyboard 是完整分镜；
- Storyboard 包含已经定义完成的 Shot；
- Shot 引用项目资产；
- Shot 是 Preview 和 Video Clip 的来源；
- Video Clip 按照 Shot 顺序形成最终有序视频素材。

---

## 5. 状态与生命周期

### Character / Scene / Prop

```text
Draft → Confirmed
          │
          └── 修改 → Needs Confirmation → Confirmed
```

规则：

- 新建立的关键资产默认未确认；
- 未确认资产不能作为正式资产进入后续制作；
- 用户确认后进入 Confirmed；
- 已确认资产的关键定义发生修改后，需要重新确认；
- 重新确认之前，系统应提示已有下游内容可能受到影响；
- 不自动重新生成已有下游内容。

---

### Script

```text
Not Generated → Generated
                    │
                    ├── Modified
                    │
                    └── Regenerated
```

规则：

- Script 可以直接提供或从 Story 转换；
- Script 可以人工修改；
- Story 修改后，已有 Script 可能受到影响；
- 是否重新生成由用户决定。

---

### Storyboard

```text
Not Generated → Generated
                    │
                    ├── Modified
                    │
                    └── Regenerated
```

规则：

- Storyboard 来源于 Script；
- Storyboard Generated 时，完整 Shot List 同时存在；
- Script 修改后，已有 Storyboard 可能受到影响；
- 是否重新生成 Storyboard 由用户决定。

---

### Shot

```text
Defined
   │
   ├── Preview Generated      (可选)
   │
   └── Video Generated
```

规则：

- Shot 在 Storyboard 生成时进入 Defined；
- Defined 表示 Shot 已具有完整文字定义；
- Preview 不属于必须经历的状态；
- Shot 可以从 Defined 直接进入视频生成；
- Shot 可以存在多个 Preview 和 Video Clip。

---

### 下游影响状态

```text
Current → Possibly Affected
              │
              └── 用户处理后 → Current
```

规则：

- 上游内容发生修改时，相关下游结果可以被标记为 Possibly Affected；
- Possibly Affected 表示“可能与最新上游定义不一致”；
- Possibly Affected 不表示内容自动失效；
- 系统不得仅因为该状态自动删除或重新生成已有结果；
- 是否重新生成由用户决定。

---

## 6. 领域不变量

- Story 如存在，应作为原始内容保留，不能被 Script 或其他生成结果覆盖；
- 正式生产必须具有已确定的 Script；Script 可以直接提供，也可以由 Story 转换；
- Script 是正式领域对象，而不是临时 AI 输出；
- Storyboard 生成完成时必须同时存在完整 Shot List；
- 不存在独立的“Storyboard 生成完成后再生成 Shot List”的业务阶段；
- Shot 必须属于一个 Storyboard；
- Shot 是视频生成的基本业务单位；
- Shot 必须具有足够完整的文字定义，能够作为视频生成的基本输入；
- Preview 是可选内容，不是 Video Clip 的强制前置条件；
- Character、Scene、Prop 是 Project 级可复用资产；
- Character、Scene、Prop 必须经过用户确认后才能作为正式资产进入后续制作；
- 同一角色、场景或道具跨章节出现时，应优先复用已有资产；
- Shot 引用 Character、Scene、Prop，不重复定义它们的稳定身份和视觉特征；
- 同一个 Shot 可以拥有多个 Preview 或 Video Clip 候选结果；
- 重新生成不能强制覆盖已有候选结果；
- 上游修改可以使下游内容进入“可能受影响”状态，但不能自动决定重新生成；
- 是否重新生成受到影响的下游内容由用户决定；
- 固定 Prompt 模板不是 Character、Scene、Prop、Script、Storyboard 或 Shot 的领域内容。

---

## 7. 领域事件（可选）

| 事件 | 含义 | 触发条件 |
| --- | --- | --- |
| Asset Identified | 从已确定的剧本识别资产需求 | Script 资产提取发现新的或变化的资产需求 |
| Asset Confirmed | 关键资产已经被用户确认 | 用户确认 Character、Scene 或 Prop |
| Asset Changed | 已有关键资产定义发生修改 | 用户修改已存在的 Character、Scene 或 Prop |
| Script Established | 正式剧本已确定 | 用户确认直接提供的剧本，或确认由 Story 转换的剧本 |
| Script Changed | 已有 Script 内容发生变化 | 用户修改或重新生成 Script |
| Storyboard Generated | Script 已生成完整 Storyboard 和 Shot List | Storyboard 生成完成 |
| Storyboard Changed | Storyboard 或其中 Shot 定义发生变化 | 用户修改或重新生成 Storyboard |
| Preview Generated | 某个 Shot 产生新的静态预览 | Shot 预览生成完成 |
| Video Clip Generated | 某个 Shot 产生新的视频候选结果 | Shot 视频生成完成 |
| Result Selected | 用户选择某个候选结果作为当前使用结果 | 用户选择 Preview 或 Video Clip |
| Downstream Affected | 下游内容可能与最新上游定义不一致 | 相关上游内容发生修改 |

---

## 8. 领域边界 / 子域

当前领域可以按职责理解为以下几个部分。

### 内容域

负责从原始故事逐步形成可制作内容：

```text
Story（可选）→ Script → 资产识别与确认 → Storyboard → Shot
```

核心概念：

- Chapter；
- Story；
- Script；
- Storyboard；
- Shot。

---

### 资产域

负责建立和维护跨章节复用的稳定内容：

```text
Character
Scene
Prop
Visual Asset
```

核心职责：

- 资产识别；
- 资产定义；
- 视觉固化；
- 用户确认；
- 跨章节复用。

---

### 生成结果域

负责 Shot 对应的视觉生成结果：

```text
Shot
├── Preview
└── Video Clip
```

核心职责：

- 保存候选结果；
- 支持多次生成；
- 选择当前使用结果。

---

### 依赖与变更域

负责表达：

> 上游内容发生变化后，哪些已有内容可能受到影响。

核心职责：

- 表达依赖关系；
- 标记可能受影响的内容；
- 将重新生成决定留给用户。

当前这些划分主要用于帮助理解职责。

是否在软件架构中形成对应模块，由后续架构设计决定。

---

## 9. 示例

以故事角色“林尘”为例。

第一章可选 Story：

> 林尘背着柴从青云山返回村庄，在路边发现一块神秘玉佩。

剧本确定并识别资产需求后：

```text
Project
├── Character
│   └── 林尘
│
├── Scene
│   ├── 青云山
│   └── 山林小路
│
└── Prop
    └── 神秘玉佩
```

用户匹配已有资产，并生成、确认新增资产后，它们成为 Project 级正式资产。

第一章可以直接提供 Script，或由 Story 转换得到 Script。正式资产识别应在 Script 确定后进行。

Script 再生成 Storyboard：

```text
Storyboard

Shot 01
  场景：青云山 / 山林小路
  角色：林尘
  动作：背着柴沿山路向村庄方向行走
  景别：远景
  摄像机：缓慢跟随
  时间：黄昏
  时长：7 秒

Shot 02
  场景：山林小路
  角色：林尘
  道具：神秘玉佩
  动作：林尘停下脚步，发现路边玉佩
  景别：中景转近景
  时长：7 秒
```

此时：

> Storyboard 已经生成完成，Shot 01 和 Shot 02 也已经具有完整文字定义。

后续不再进行一次独立的“生成 Shot”操作。

对于 Shot 01，可以：

```text
Shot 01
   │
   └── Video Clip
```

也可以：

```text
Shot 01
   │
   ├── Preview
   │
   └── Video Clip
```

第二章再次出现林尘时：

```text
Chapter 02
   │
   └── Shot
        └── references → Character: 林尘
```

继续引用已有 Character，而不是重新创建“林尘”。

---

## 10. 待确认问题

- Project 是否就是完整作品边界，还是未来需要在 Project 之上增加 Series / Work 等更高层概念？
- 可选 Story 是否需要独立身份，还是作为 Chapter 的原始内容即可？
- Script 内部是否需要进一步定义场次、剧情段落或其他结构？
- 一个 Chapter 是否只维护一个当前 Storyboard，还是允许同时存在多个完整 Storyboard 方案？
- Shot 的字段是否需要进一步区分“剧情事实”“镜头设计”和“视频生成描述”？
- Character 的换装、受伤、年龄变化等状态如何表达？
- 是否需要正式引入 Character Variant？
- Scene Variant 是否应该成为正式核心概念？
- Prop 是否需要 Variant？
- Visual Asset 是否应该统一覆盖 Character、Scene、Prop 的所有视觉参考？
- Preview 是否属于 Visual Asset 的一种，还是保持独立概念更清晰？
- Character、Scene、Prop 的确认是确认“业务定义 + 当前视觉资产”，还是两者分别确认？
- Story 修改后，已确定的 Script 是否需要提示可能受影响（不自动覆盖）？
- Storyboard 中单独修改一个 Shot 时，Storyboard 整体是否需要产生新的版本？
- 上下游影响关系需要精确到整个对象，还是进一步精确到具体字段？
- “当前选中的 Video Clip”是否属于 Shot 的状态，还是 Video Clip 自身的选用状态？
