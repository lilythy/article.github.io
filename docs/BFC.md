##  BFC定义
BFC(Block formatting context)直译为"块级格式化上下文"。它**是一个独立的渲染区域**，只有Block-level box（如下图）参与， 它规定了内部的Block-level Box如何布局，并且与这个区域外部毫不相干。  
![](https://lc-gold-cdn.xitu.io/b80801d8707be24ecbc0?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)  
我们常说的文档流其实分为定位流、浮动流和普通流三种。而**普通流其实就是指BFC中的FC**。  

**FC**是formatting context的首字母缩写，直译过来是**格式化上下文**，它**是页面中的一块渲染区域**，有一套渲染规则，决定了其子元素如何布局，以及和其他元素之间的关系和作用。  
常见的FC有BFC、IFC（行级格式化上下文），还有GFC（网格布局格式化上下文）和FFC（自适应格式化上下文）
# 触发BFC的条件
满足下列条件之一就可触发BFC  
+ 根元素，即HTML元素
+ float的值不为none
+ overflow的值不为visible，为 auto、scroll、hidden
+ display值为 inline-block、table-cell、table-caption、table、inline-table、flex、inline-flex、grid、inline-grid
+ position的值为absolute或fixed

# BFC布局规则
- 内部的Box会在垂直方向，一个接一个地放置
- Box垂直方向的距离由margin决定。属于同一个BFC的两个相邻Box的margin会发生重叠
- 每个元素的左外边距与包含块的左边界相接触（从左向右），即使浮动元素也是如此。（这说明BFC中子元素不会超出他的包含块，而position为absolute的元素可以超出他的包含块边界）
- BFC的区域不会与float的元素区域重叠
- BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。反之也如此。
- 计算BFC的高度时，浮动元素也参与计算

# BFC的作用
- 1、有时候因为多列布局采用小数点位的width导致因为浏览器因为四舍五入造成的换行的情况，可以在最后一列触发BFC的形式来阻止换行的发生。
- 2、可以阻止元素被浮动元素覆盖：一个正常文档流的block元素可能被一个float元素覆盖，挤占正常文档流，因此可以设置一个元素的float、display、position值等方式触发BFC，以阻止被浮动盒子覆盖。
- 3、清除内部浮动：通过改变包含浮动子元素的父盒子的属性值，触发BFC，以此来包含子元素的浮动盒子。
- 4、分属于不同的BFC时可以阻止margin重叠：属于同一个BFC的两个相邻块级子元素的上下margin会发生重叠，(设置writing-mode:tb-rl时，水平margin会发生重叠)。所以当两个相邻块级子元素分属于不同的BFC时可以阻止margin重叠。
这里给任一个相邻块级盒子的外面包一个div，通过改变此div的属性使两个原盒子分属于两个不同的BFC，以此来阻止margin重叠。  

<!-- 参考链接：https://juejin.cn/post/6844903476774830094 -->
