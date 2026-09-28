# 功能规格（Feature Specifications）

一个 SPEC 是**一个完整功能或 Vertical Slice 的实现契约**。

当任务已经大到或模糊到不适合直接根据一段聊天提示开始编码时，应先创建 SPEC。SPEC 应随着已确认行为的变化而更新，并尽量靠近它所约束的代码演进。

一个好的 SPEC 应回答：

- 这个 Slice 解决什么问题？
- 哪些内容明确属于范围内，哪些不属于？
- 它实现了哪些 Use Case / Domain 概念？
- 输入、输出、业务规则和失败行为是什么？
- 创建或修改了哪些稳定契约？
- 如何验证它已经正确完成？

SPEC 通常不应该重新复制全局架构。需要时引用 `ARCHITECTURE.md` 和相关 ADR。

创建新 SPEC 时复制 `TEMPLATE.md`，例如：

```text
docs/specs/asset-extraction.md
docs/specs/user-import.md
docs/specs/report-export.md
```

建议一个 SPEC 对应一个足够小、能够独立实现和验收的 Vertical Slice，而不是一个庞大的“整个子系统”。
