# 单向数据流&双向数据流
## 单向数据流代码：Flux 架构
Flux 并不是一个具体的框架，它是一套由 Facebook 技术团队提出的应用架构，这套架构约束的是应用处理数据的模式。在 Flux 架构中，一个应用将被拆分为以下 4 个部分。

**View（视图层**：用户界面。该用户界面可以是以任何形式实现出来的，React 组件是一种形式，Vue、Angular 也完全 OK。Flux 架构与 React 之间并不存在耦合关系。

**Action（动作）**：也可以理解为视图层发出的“消息”，它会触发应用状态的改变。

**Dispatcher（派发器）**：它负责对 action 进行分发。

**Store（数据层）**：它是存储应用状态的“仓库”，此外还会定义修改状态的逻辑。store 的变化最终会映射到 view 层上去。

这 4 个部分之间的协作将通过下图所示的工作流规则来完成配合
![](https://s0.lgstatic.com/i/image/M00/7E/D2/CgqCHl_PX4iAVQDeAABqpNRcHXQ065.png)