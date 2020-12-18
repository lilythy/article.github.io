# redux 
React管理数据有props 和 state:
- props意味着父级分发下来的属性
- state意味着组件内部可以自行管理的状态，并且整个React没有数据向上回溯的能力，这就是react的单向数据流 

如果管理一个数据状态非常复杂的应用，用props 和 内部state 就会很鸡肋，这时候就需要把所有的state集中到组件顶部，能够灵活的将所有state各取所需的分发给所有的组件，是的，这就是redux。

Redux的定义是 JavaScript 状态容器，他的单一数据流提供了 可预测化[^1] 的状态管理。也是Flux架构的一种实现。  
> [^1]:理解 **可预测** 的含义可以参考[单向数据流&双向数据流](https://lilythy.github.io/article.github.io/docs/dataFlow)  

  # dva
dva 首先是一个基于 redux 和 redux-saga 的数据流方案，然后为了简化开发体验，dva 还额外内置了 react-router 和 fetch，所以也可以理解为一个轻量级的应用框架。dva只是基于现有开源框架的一层轻量封装，并没有引入任何新概念
+ React：管理View
+ react-router：管理路由
+ Redux：管理Model
+ redux-saga：管理异步调用（副作用）

他的框架结构图也很熟悉
![dva架构图](https://user-gold-cdn.xitu.io/2019/5/9/16a984c61c9bdd4c?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

dva的优势在于：  
1. **快速初始化**: 可以快速实现项目的初始化，不需要繁琐地配置
2. **简化开发**：将initState、saga、reducer集成到一个model里面统一管理，避免文件散落在各个文件里面，便于快速查找与开发
3. **简洁的API**：整个项目中只有dva、app.model、app.router、app.use、app.start几个API

# Royjs  
Royjs具备其他框架或者状态管理不具备的局部状态自动挂载到全局的能力，使用方式如下：
```javascript
import {Store, Provider, inject} from '@royjs/core';
const listStore = new Store({
    name: 'list',
    actions: {
        fetch() {
            
        }
    }
});
const globalStore = new Store();
@inject(listStore)
class List extends React.Component {
    render() {
    }
}
// List可以自己渲染而不依赖全局的store
ReactDOM.render(<List/>, root) 
// List在全局store下面后，会自动挂载到globalStore上，便于和其他模块进行通信
ReactDOM.render(<Provider store={globalStore}><List/></Provider>, root);
```

**与dva的不同**
1. Royjs采用了响应式的状态更新策略，而非每次返回一个新的对象， 并且只会对依赖这个状态的组件进行diff计算。
2. Royjs的action同时可以书写异步或者同步的action，没有明显的区别。dva需要借助于redux-saga的中间件才具备异步能力。

**与Mobx的不同**
1. Mobx采用了defineProperty的方式实现响应式数据，而Royjs从1.0版本开始，使用了Proxy实现响应式数据。
2. Mobx需要显示的声明Store中的对象为监听对象，而Royjs不需要
3. Royjs借鉴了Redux中需要dispatch的动作，便于追溯