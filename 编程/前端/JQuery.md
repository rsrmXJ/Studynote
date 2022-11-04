# JQuery

### 1. JQuery 介绍

1\. 什么是 JQuey

- JQuery 就是 JavaScript 和 Query (查询)。
- 他是辅助 JavaScript 开发的类库

2\. 核心思想

write less, do more (写得更少，做得更多)，它实现了很多历览器的兼容问题

3\. jQuery 现在已经成为最流行的 JavaScript 库，

4\. 好处：

jQuery 是免费、开源的，jQuery 的语法设计可以使开发更加便捷，例如操作文档对象、选择 DOM 元素、 制作动画效果、事件处理、使用 Ajax 以及其他功能

### 2. 使用 JQuery

```CSS
//第一步引入 JQuery 类库
<script src="../script/jquery-1.7.2.js"></script>

用 JavaScript 写

window.onload = function () {
    var btnObj = document.getElementById("btnId");
    // alert(btnObj);//[object HTMLButtonElement] ====>>> dom 对象
    btnObj.onclick = function () {
        alert("js 原生的单击事件");
    }
}

//用 JQuery
$(function () { // 表示页面加载完成 之后，相当 window.onload = function () {}
    var $btnObj = $("#btnId"); // 表示按 id 查询标签对象
    $btnObj.click(function () { // 绑定单击事件
        alert("jQuery 的单击事件");
    });
});
```

JQuery 中 $ 是什么？

$ 是一个函数

注意：使用 JQuery 必须要引入 JQuery 库

- 传入参数为【函数】

  表示页面加载完成之后，执行。相当于  `window.onload = function(){}`	

- 传入参数为 【HTML 字符串】时

  会创建 HTML 标签对象

```
$("<div>" +
    "    <span>asdfasdf</span>" +
    "    <span>asdfasdfasdf</span>" +
    "</div>").appendTo("body");
//创建对象，并添加到 body 抱歉中。
```

- 传入参数为 【选择器字符串】时

  会根据对应选择器，选择标签对象

- 传入参数为 DOM 对象时，会把 DOM 对象转换为 JQuery 对象

### 3. 过滤选择器

其它的选择器 和 CSS 一致。

#### 1. 基本过滤选择器

- `:first` 获取匹配的第一个元素。

  例如：`$('li:first')` 获取 li 的第一个元素

- :last  获取匹配的最后一个元素

- :not(:tarrbuite=value) 去除所有与给定选择器匹配的元素 

  例如`$(input[type=checkbox]:not(:checked))`

- :even 匹配索引值为偶数的元素，从 0 开始计数

- :odd 匹配所有索引值为奇数的元素，从 0 开始奇数

- :eq(index) 匹配一个给定索引值的元素，从 0 开始计数

- :gt(index) 匹配所有大于给定索引值的元素

- :lt(index) 匹配所有小于给定索引值的元素

- :headr 匹配标题元素，如 h1,h2,h3…

- :animated 匹配所有正在执行动画i效果的元素

#### 2. 内容过滤选择器

- :contains(text) 匹配包含指定文本的元素
- :empty 匹配所有不包含子元素或者文本的空元素
- :parent 匹配含有子元素或者文本的元素
- :has(selector) 匹配包含有选择器所匹配的元素的元素

```html
HTML代码：
<div><p>Hello</p></div>
<div>Hello again</div>
JQuery 代码：
$("div:has(p)"); //匹配包含 p 标签的 div 标签
```

#### 3. 属性过滤选择器

- [attribute] 匹配包含给定元素的属性
- [attribute=value] 匹配给定属性是某个特定值的元素
- [attribute!=value] 
  - 匹配不含有指定属性的元素
  - 匹配给定属性的属性值不等于特定值的元素
- [attribute^=value] 匹配给定的属性的属性值是以某些值开始的元素
- [attribute$=value] 匹配给定的属性的属性值是以某些值结尾的元素
- [attribute*=value] 匹配给定的属性的属性值包含某些值的元素
- \[selector1\]\[selector2\]\[selectorN\] 复合属性选择器，需要同时满足多个条件时使用

#### 4. 表单过滤选择器

- :input 匹配所有 input、textarea、select 和 button 元素
- :text 匹配所有的单行文本框
- :password 匹配所有的密码框
- :radio 匹配所有的单选按钮
- :checkbox 匹配所有的复选框
- :submit 匹配所有提交按钮
- :reset 匹配所有重置按钮
- :image
- :file
- :hidden 匹配所有不可见的元素或 type=hidden  的元素

表单对象属性

- :enable 匹配所有可用的元素
- :disable 匹配所有不可用的元素

方法：

- val(String) 设置或获取表单的 value 值。

  无参：获取	有参：设置

- each() 是 JQuery 对象提供用来遍历元素的方法

  在遍历的 function 函数中，有一个 this 对象，这个 this 对象，就是当前遍历到的 dom 对象

  例子：

  ```javascript
  	var $checkboies = $(":checkbox:checked");
  
      $checkboies.each(function () {
              alert( this.value );
      });
  ```

### 4. 元素筛选

- eq()	获取给定索引的元素
- first()  获取第一个元素
- last()   获取最后一个元素
- filter(exp) 留下匹配的元素
- is() 判断是否匹配给定的选择器
- has(exp) 返回包含有匹配选择器的元素的元素
- not(exp) 补选中指定选择器的元素
- children(exp) 匹配给定选择器的子元素
- find(exp)  匹配给定选择器的后代元素
- next() 当前元素的下一个元素
- nextAll() 当前元素后面的所有兄弟元素
- nextUntil() 返回当前元素到指定
- parent() 父元素
- prev() 当前元素的前一个兄弟元素
- prevAll() 当前元素前面的所有兄弟元素
- prevUnitil(exp) 返回当前元素到指定匹配的元素为止的前面元素
- siblings(exp) 所有兄弟元素
- add() 把 add 匹配的选自其的元素添加到当前 JQuery 中

以上的方法和 基本选择器的哦功能一致

### 5.  属性操作

- html() 设置和获取其实标签和结束标签之间的**内容**

- text() 设置和获取其实标签和结束标签中的**文本**

- val() 设置和获取表单项的 value 的属性

  可以同时设置多个表单项的选中状态

  `$(":radio,:checkbox,#multiple").val("radio2","checkbox1","checkbox2","checkbox3");  `

- attr() 设置和获取属性的值，不推荐操作 checked、readOnly、selected、disabled 等

  attr 方法还可以操作非标准的属性，如自定义属性

- prop() 设置和获取属性的值，只推荐操作 checked、readOnly、selected、disabled 等

### 6. DOM 的增删改查

内部插入

- a.appendTo(b) 把元素 a 插入到 b 元素子元素的末尾，成为最后一个子元素
- a.prependTo(b)  把元素 a 插入到 b 元素子元素的前面，成为第一个子元素

外部插入

- insertAfter()
- insertBefore()

替换

- a.replaceWith(b)  b 替换 a （只替换为一个 b）
- replaceAll()            a 替换所有 b (有几个 b 替换几个 a)

删除

- remove() 例如：`a.remove()l;` 删除 a 标签
- empty() 例如：`a.empty(); `清空 a　标签的内容

### 7. CSS 样式操作

- addClass() 添加样式（实际上就是添加了类，我们是通过类来设置样式的）
- removeClass() 删除样式
- toggleClass() 有就删除，没有就添加
- offest() 设置和获取元素的坐标

### 8. 动画操作

基本动画：

- show() 将隐藏的元素显示
- hide() 将显示的元素隐藏
- toggle() 隐藏就显示，显示就隐藏

以上动画方法都可以添加参数

- 第一个参数，动画执行的时长，以 ms 为单位
- 第二个参数，动画的回调函数（动画完成后自动调用的函数）

淡入淡出动画：

- fadeIn() 淡入
- fadeOut() 淡出
- fadeTo()  在指定时长内慢慢将透明度修改到指定的值。0(可见)~!(透明)
- fadeToggle() 淡入/淡出

### 9. JS 和 JQuery  的区别？

