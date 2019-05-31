# layout 实现三栏布局的六种方式

## 圣杯布局

圣杯布局是指布局从上到下分为 header、container、footer，然后 container 部分定为三栏布局。

基础 HTML：

```
<body>
    <div class="container">
        <!-- 优先渲染 -->
        <div class="center">
            center
        </div>
        <div class="left">
            left
        </div>
        <div class="right">
            right
        </div>
    </div>
</body>

```

基础 CSS：

```
 .container {
        overflow: hidden;
    }
    .container > div {
        position: relative;
        float: left;
        height: 100px;
    }
    .center {
        width: 100%;
        background-color: red;
    }
    .left {
        width: 200px;
        background-color: green;
    }
    .right {
        width: 200px;
        background-color: blue;
    }
```

对于 container，给它设置一个 overflow: hidden 使其成为一个 `BFC`，使三栏浮动，并相对定位，给左右两个容器设置 200px 的宽度中间的容器设置 100%的宽度。

此时 left 和 right 被相对于父元素 container 宽度的 100%的 center 挤到下面。

BFC 的作用：

- 消除 Margin Collapse
- 容纳浮动元素
- 阻止文本换行

步骤：

- 把 left 放上去，由于浮动的关系，给 left 设置 `margin-left: -100%` 即可使左侧栏浮动到 center 上面，并位于 center 左侧之上。
- 同样为 right 设置 `margin-left: -200px`，这里的长度等于 right 的长度
- 这时 center 的两侧被 left 和 right 覆盖了，因此给 container 设置 `padding: 0 200px`
- 由于 left 和 right 也同时往中间缩，因此给 left 和 right 分别设置 `left: -200px; right: -200px`，往两侧分别偏移自身的宽度去覆盖掉 contaniner 使用 padding 后的空白位置。

这时，圣杯布局就完成了，但是在拖到很小的时候，布局会乱，以下是最终样式。

```
.container {
  overflow: hidden;
  padding: 0 200px;
}
.container > div {
  position: relative;
  float: left;
  height: 100px;
}
.center {
  width: 100%;
  background-color: red;
}
.left {
  width: 200px;
  background-color: green;
  margin-left: -100%;
  left: -200px;
}
.right {
  width: 200px;
  background-color: blue;
  margin-left: -200px;
  right: -200px;
}

```

## 双飞翼布局

这种布局方式同样分为 header、container、footer。圣杯布局的缺陷在于 center 是在 container 的 padding 中的，因此宽度小的时候会出现混乱。

双飞翼布局给 center 部分包裹了一个 main 通过设置 margin 主动地把页面撑开。
基础 HTML：

```
<div class="container">
  <!-- 优先渲染 -->
  <div class="center">
    <div class="main">
      center
    </div>
  </div>
  <div class="left">
    left
  </div>
  <div class="right">
    right
  </div>
</div>

```

**区别**：-> 第三步：给 center 设置 margin: 0 200px

这时窗口宽度过小时就不会出现混乱的情况了，关键点在于内容部分是包裹在 main 中。
以下是最终样式：

```
    .container {
      overflow: hidden;
      padding: 0 200px;
    }
    .container > div {
      float: left;
      height: 200px;
      position: relative;
    }
    .center {
      background: red;
      width: 100%;
    }
    .left {
      width: 200px;
      background: green;
      margin-left: -100%;
      left: -200px;
    }
    .right {
      width: 200px;
      background: blue;
      margin-left: -200px;
      right: -200px;
    }
```

## flex 布局

Flex 布局是由 CSS3 提供的一种方便的布局方式。

基础 HTML：

```
<body>
    <div class="container">
        <!-- 优先渲染 -->
        <div class="center">
            center
        </div>
        <div class="left">
            left
        </div>
        <div class="right">
            right
        </div>
    </div>
</body>
```

步骤：

- 给 container 设置为一个 flex 容器 `display: flex`

- center 的宽度设置为 `width: 100%，left 和 right 设置为 width: 200px`

- 为了不让 left 和 right 收缩，给它们设置 `flex-shrink: 0`

- 使用 order 属性给三个部分的 DOM 结构进行排序：`left：order: 1，center：order: 2，right：order: 3`

**flex-shrink:**定义项目的缩小比例，默认为 1，如果空间不足则项目缩小，如果有一项为 0，其他为 1，当空间不足时，前者不缩小。

可以看到，flex 布局是一种极其灵活的布局方式。以下是最终样式：

```
    .container {
      display: flex;
    }
    .center {
      width: 100%;
      order: 2;
      background: red;
    }
    .left {
      width: 200px;
      order: 1;
      background: green;
      flex-shrink: 0;
    }
    .right {
      width: 200px;
      order: 3;
      background: blue;
      flex-shrink: 0;
    }

```

## 绝对定位布局

步骤：

1. 给 container 设置 position: relative 和 overflow: hidden，因为绝对定位的元素的参照物为第一个 postion 不为 static 的祖先元素。
2. left 向左浮动，right 向右浮动。
3. center 使用绝对定位，通过设置 left 和 right 并把两边撑开。
4. center 设置 top: 0 和 bottom: 0 使其高度撑开

**这种方式的缺点是依赖于 left 和 right 的高度，如果两边栏的高度不够，中间的内容区域的高度也会被压缩**

基础 HTML：

```
    <div class="container">
      <!-- 优先渲染 -->
      <div class="center">
        center
      </div>
      <div class="left">
        left
      </div>
      <div class="right">
        right
      </div>
    </div>

```

css:

```
    .container {
      overflow: hidden;
      position: relative;
    }
    .center {
      position: absolute;
      left: 200px;
      right: 200px;
      background: red;
      top: 0;
      bottom: 0;
    }
    .left {
      float: left;
      width: 200px;
      background: green;
      float: left;
      left: 0;
    }
    .right {
      float: right;
      width: 200px;
      background: blue;
    }
```

## table-cell 布局

表格布局的好处是能使三栏的高度统一。

基础 HTML：

```
<body>
    <div class="container">
        <div class="left">
            left
        </div>
        <!-- 这时的center要放在中间 -->
        <div class="center">
            center
        </div>
        <div class="right">
            right
        </div>
    </div>
</body>

```

步骤：

- 给三栏都设置为表格单元 display: table-cell
- left 和 right width: 200px，center width: 100%
- 这时 left 和 right 被挤到两边去了，因此要分别设置 min-width: 200px 确保不会被挤压。

```
    .container {
        overflow: hidden;
        position: relative;
    }
    .container > div {
        display: table-cell;
        height: 100%;
    }
    .center {
        margin: 0 200px;
        width: 100%;
        background: red;
    }
    .left {
        width: 200px;
        min-width: 200px;
        background-color: green;
    }
    .right {
        width: 200px;
        min-width: 200px;
        background-color: blue;
    }

```

**这种布局方式能使得三栏的高度是统一的，但不能使 center 放在最前面得到最先渲染。**

## 网格布局

网格布局可能是最强大的布局方式了，使用起来极其方便，但目前而言，兼容性并不好。网格布局，可以将页面分割成多个区域，或者用来定义内部元素的大小，位置，图层关系。

基础 HTML：

```
<body>
    <div class="container">
        <div class="left">
            left
        </div>
        <!-- 这时的center要放在中间 -->
        <div class="center">
            center
        </div>
        <div class="right">
            right
        </div>
    </div>
</body>

```

步骤：

- 给 container 设置为 `display: grid`
- 设置三栏的高度：`grid-template-rows: 100px`
- 设置三栏的宽度，中间自适应，两边固定：`grid-template-columns: 200px auto 200px;`

```
    .container {
        display: grid;
        width: 100%;
        grid-template-rows: 100px;
        grid-template-columns: 200px auto 200px;
    }
```

## 总结

- 圣杯布局、双飞翼布局、flex 布局的高度取决于内容区(center 部分)，页面的高度取决于内容区
- 绝对定位的内容区高度取决于两边栏的最高点。
- table-cell 布局能让三栏的高度一致，但不能优先渲染 center。
- 网格布局极其强大，但兼容性差
