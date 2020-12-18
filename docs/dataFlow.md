# 单向数据流&双向数据流
## 单向数据流核心思想：Flux 架构
Flux 并不是一个具体的框架，它是一套由 Facebook 技术团队提出的应用架构，这套架构约束的是应用处理数据的模式。  

**Flux的核心思想就是数据和逻辑永远单向流动。** 在 Flux 架构中，一个应用将被拆分为以下 4 个部分：

**View（视图层**：用户界面。该用户界面可以是以任何形式实现出来的，React 组件是一种形式，Vue、Angular 也完全 OK。**Flux 架构与 React 之间并不存在耦合关系**。

**Action（动作）**：也可以理解为视图层发出的“消息”，它会触发应用状态的改变。

**Dispatcher（派发器）**：它负责对 action 进行分发。

**Store（数据层）**：它是存储应用状态的“仓库”，此外还会定义修改状态的逻辑。store 的变化最终会映射到 view 层上去。

这 4 个部分之间的协作将通过下图所示的工作流规则来完成配合
![](https://s0.lgstatic.com/i/image/M00/7E/D2/CgqCHl_PX4iAVQDeAABqpNRcHXQ065.png)
一个典型的 Flux 工作流是这样的：用户与 View 之间产生交互，通过 View 发起一个 Action；Dispatcher 会把这个 Action 派发给 Store，通知 Store 进行相应的状态更新。Store 状态更新完成后，会进一步通知 View 去更新界面。这个数据流向路线是单向不可逆的。  

**那么单向数据流到底是为了解决什么问题呢？**  
这就需要了解双向数据流带来了什么问题。  

# 双向数据流
**什么是双向数据流？**  

双向数据流用通俗的话讲就是 无论数据改变，或是用户操作，都能带来互相的变动。架构图如下：
![](https://intranetproxy.alipay.com/skylark/lark/0/2020/png/87051/1607516716802-4dc77034-04cb-4ae8-af92-68d88ff9bd3d.png)  

**双向数据流存在什么问题？**  
这种架构在单一的model和view时没什么问题，当有多个model和view时就会变得非常混乱了，体感如下：

![](https://s0.lgstatic.com/i/image/M00/7E/C5/CgqCHl_PVgWAcAkZAAFInClVHRM354.png)

这还只是只有一个Controller的情况，如果还存在多个Controller的话，将会比上图复杂的多。如此混乱的修改来源，将会使得我们连 Bug 排查都无从下手，因为你很难区分出一个数据的变化到底是由哪个 Controller 或者哪个 View 引发的。  

再回过头来看Flux **严格的单向数据流**,在单向数据流下，s数据状态的变化是可预测的。如果 store 中的数据发生了变化，那么有且仅有一个原因，那就是由 Dispatcher 派发 Action 来触发的。这样一来，就从根本上避免了混乱的数据关系，使整个流程变得清晰简单。  
然而flux也不是完美无瑕的，除了开发者的学习成本会提升外，Flux 架构还意味着项目中代码量的增加。