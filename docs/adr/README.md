# 架构决策记录（ADR）

ADR（Architecture Decision Record）用于记录**存在有意义替代方案、会产生长期影响，并且未来维护者可能重新质疑的重要决策**。

适合记录为 ADR 的内容包括：

- 模块职责和所有权边界；
- 持久化、消息或任务执行策略；
- 公共契约策略；
- 重要框架、依赖或基础设施选择；
- 未来贡献者很可能误改或推翻的关键设计决定。

以下内容通常**不需要** ADR：

- 普通实现细节；
- 格式化选择；
- 无长期影响的小型库使用；
- 每一个微小技术决定。

## 命名

```text
0001-short-decision-title.md
0002-next-decision.md
```

编号不重复使用。

已经 Accepted 的 ADR 是历史记录。如果未来改变决定，不应直接改写旧 ADR 来掩盖历史，而应创建新的 ADR，并通过 `Supersedes / Superseded by` 表明替代关系。

创建 ADR 时复制 `TEMPLATE.md`。
