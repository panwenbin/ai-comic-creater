# AI 项目模板

一个与技术栈无关的 AI 辅助软件开发仓库模板。

**仓库是项目的权威信息来源（Source of Truth）。** 对话用于探索、讨论和形成决策；一旦决策被确认，就应写回仓库，而不是只留在聊天记录中。

## 开发模型

```text
PRODUCT
  ↓
DOMAIN
  ↓
WORKFLOW + USE CASES
  ↓
ARCHITECTURE
  ↓
选择最小可用 Vertical Slice
  ↓
SPEC → 必要时 ADR → 实现 → 测试 → Review → 同步文档 → Commit
```

## 新项目如何开始

1. 使用本模板创建新仓库。
2. 通过讨论完成 `docs/PRODUCT.md`；此阶段不要提前设计实现方案。
3. 根据已确认的产品范围完成 `docs/DOMAIN.md`。
4. 完成 `docs/WORKFLOW.md` 与 `docs/USE_CASES.md`。
5. 当前述文档足够稳定后，再完成 `docs/ARCHITECTURE.md`。
6. 选择当前最小但有实际价值的 Vertical Slice（纵向切片）。
7. 从 `docs/specs/TEMPLATE.md` 创建该功能的 SPEC。
8. 只有遇到需要长期保留的重要架构决策时，才创建 ADR。
9. 实现、测试、Review、同步文档并提交 Git。
10. 一次推进一个 Vertical Slice，持续循环。

不要在实现开始前试图一次性写完未来所有 SPEC 和 ADR。全局文档只需要稳定到足以指导当前阶段和下一个 Vertical Slice；随后根据真实实现获得的反馈继续演进。

## 文档职责

| 文件 | 职责 |
| --- | --- |
| `AGENTS.md` | AI Coding Agent 在本仓库工作时必须遵守的规则 |
| `docs/PRODUCT.md` | 产品目标、范围、用户、约束和明确不做的内容 |
| `docs/DOMAIN.md` | 领域语言、核心概念、关系、规则和不变量 |
| `docs/WORKFLOW.md` | 端到端业务流程、检查点、分支和状态变化 |
| `docs/USE_CASES.md` | 用户/系统可观察行为以及面向验收的用例 |
| `docs/ARCHITECTURE.md` | 系统边界、组件、依赖规则和运行时关注点 |
| `docs/adr/` | 需要长期保留的架构决策及其理由 |
| `docs/specs/` | 单个 Vertical Slice 的可实施规格说明 |

## 核心原则

- 先讨论，再把已确认的决策写入仓库。
- 仓库当前状态优先于聊天历史。
- 产品/领域决策与实现决策分离。
- 优先完成最小的端到端 Vertical Slice，而不是提前铺满整个技术分层。
- 稳定契约不能被静默修改。
- 测试既约束人，也约束 AI Agent。
- 文档与实现同步维护，而不是项目结束后补写。
- 没有具体需求支撑时，不提前引入抽象、依赖和基础设施。
- 全局文档描述长期事实；SPEC 描述当前功能；ADR 记录值得保留的决策理由。

## 推荐的 AI 协作方式

设计阶段可以让 Chat/AI 与你逐项讨论文档，但不要让 AI 一次性替你决定所有内容。实现阶段让 Coding Agent 读取仓库中的文档、SPEC、ADR 和测试后执行。

推荐循环：

```text
需求讨论
  ↓
确认产品/领域/行为
  ↓
形成或更新 SPEC
  ↓
必要时形成 ADR
  ↓
Agent 实现
  ↓
测试 + Review
  ↓
人工验收
  ↓
同步文档
  ↓
Git Commit
```
