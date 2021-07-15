# 在react编程性能上有什么建议?
 ## 1.使用 memo 和 PureComponent  
  
  对于简单的组件，可以不用state的话，推荐使用 PureComponent；  
  对于 React Hooks，可以使用 useMemo 作为类似的方法来防止不必要的计算工作

## 2.避免匿名函数
  
  在许多情况下，您可能会为使用匿名函数，比如：
```javascript
import React from "react";

function Foo() {
  return <button onClick={() => console.log("boop")}> // ?‍♀️ BOOP</button>;
}
```
由于没有为匿名函数分配标识符（通过 const/let/var），因此每当不可避免地再次渲染此功能组件时，它们就不会被持久化（persistent）。这会导致 JavaScript 在每次重新渲染此组件时重新分配新的内存。 

## 3.避免对象字面量  

给组件设置样式时，很多人会为了方便直接在标签上传style对象，如下
  ```javascript
  function ComponentA() {
  return (
    <div>
      HELLO WORLD
      <ComponentB style={{  {/* ?‍♀️ */}
        color: 'blue',
        background: 'gold'
      }}/>
    </div>
  );
}
  ```  
这么写的话每次重新渲染 <ComponentA> 时，都必须在内存中“创建”新的对象常量，为其重新分配内存。  

## 4.避免频繁的 Mounting/Unmounting
我们习惯于使用三元语句(或类似的语句)使组件消失：  
```javascript
import Item from './DropdownItems';
render() {
    <a>
      Our Products
      {
        this.state.isOpen
          ? <Item />
          : null
      }
    </a>
  }
```  
由于 <Item> 已从 DOM 中删除，因此可能导致浏览器重绘/重排。这些可能很昂贵，尤其是如果它导致其他 HTML 元素移动时。  
## 5.使用 shouldComponentUpdate 生命周期事件   
可以利用此事件来决定何时需要重新渲染组件。
## 6.懒加载组件
我们使用 Suspense 和 lazy 懒加载组件，如下
```javascript
import React, { lazy, Suspense } from "react";

export default class CallingLazyComponents extends React.Component {
  render() {
    
    var ComponentToLazyLoad = null;
    
    if(this.props.name == "Mayank") { 
      ComponentToLazyLoad = lazy(() => import("./mayankComponent"));
    } else if(this.props.name == "Anshul") {
      ComponentToLazyLoad = lazy(() => import("./anshulComponent"));
    }
    return (
        <div>
            <h1>This is the Base User: {this.state.name}</h1>
            <Suspense fallback={<div>Loading...</div>}>
                <ComponentToLazyLoad />
            </Suspense>
        </div>
    )
  }
}
```
上面的代码中有一个条件语句，它查找 props 值，并根据指定的条件加载主组件中的两个组件，无需一开始就加载两个组件。  
## 7.箭头函数与构造函数中的绑定
处理类时的标准做法就是使用箭头函数，使用箭头函数时会保留执行的上下文，这样我们调用它时就不需要将函数绑定到上下文。  
```javascript
import React from "react";

export default class DelayedBinding extends React.Component {
  constructor() {
    this.state = {
      name: "Mayank"
    }
  }
  
  handleButtonClick = () => {
    alert("Button Clicked: " + this.state.name)
  }
  
  render() {
    return (
      <>
        <input type="button" value="Click" onClick={this.handleButtonClick} />
      </>
    )
  }
}
```
## 8.为元素添加唯一的key
因为react判断是否重新渲染是根据key值是否相同来判断的，添加唯一的key值可以避免不必要的重新渲染。