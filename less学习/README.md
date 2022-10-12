# less学习

官网：http://lesscss.cn/

## 安装less

- cdn

```ini
<script src="//cdnjs.cloudflare.com/ajax/libs/less.js/2.5.3/less.min.js"></script>
```

- 下载资源后安装

```ini
<link rel="stylesheet/less" href="./less/style1.less">
<script src="./public/less.min.js"></script>
```

！！！注意：引入和使用脚本顺序为：先`link`调用自身脚本，再cdn或资源`script`引入

- 或者npm安装

vue2安装示例

```
npm install less less-loader@5 --save --legacy-peer-deps
```

详见官网

## 变量

- 变量作用域：**就近原则**

- 支持变量的嵌套定义

  - ```less
    @fnord:  "I am fnord.";
    @var:    "fnord";
    #wrap::after{
      content: @@var; //将@var替换为其值 content:@fnord;
    }
    ```

    

1. **值变量**

以 `@` 开头 定义变量，并且使用时 直接 键入 `@`名称。

```less
/* Less */
@color: #999;
@bgColor: skyblue;//不要添加引号
@width: 50%;
#wrap {
  color: @color;
  background: @bgColor;
  width: @width;
}

/* 生成后的 CSS */
#wrap {
  color: #999;
  background: skyblue;
  width: 50%;
}
```

可以将变量维护在一个文件中`variable.less`

然后在less中使用@import引入

```
@import url(./variable.less);
```



2. **选择器变量**

```less
/* Less */
@mySelector: #wrap;
@Wrap: wrap;
@{mySelector}{ //变量名 必须使用大括号包裹
  color: #999;
  width: 50%;
}
.@{Wrap}{
  color:#ccc;
}
#@{Wrap}{
  color:#666;
}

/* 生成的 CSS */
#wrap{
  color: #999;
  width: 50%;
}
.wrap{
  color:#ccc;
}
#wrap{
  color:#666;
}
```



3. **属性变量**

```less
/* Less */
@borderStyle: border-style;
@Soild:solid;
#wrap{
  @{borderStyle}: @Soild;//变量名 必须使用大括号包裹
}

/* 生成的 CSS */
#wrap{
  border-style:solid;
}

```



4. **url变量**

需要注意，若变量存在于另一个less文件下，则图片的相对路径以最后该变量引入的目的位置为起点的相对路径，而不是是以当前less文件为起点

```less
/* Less */
@images: "../img";//需要加引号
body {
  background: url("@{images}/dog.png");//变量名 必须使用大括号包裹
}

/* 生成的 CSS */
body {
  background: url("../img/dog.png");
}

```



5. **声明变量（规则集合变量，函数变量）**

声明方式为`@name:{规则条目};`

使用`@name();`

```less
/* Less */
@borderStyle: {
    border: 2px solid black;
    border-radius: 10%;
};
//使用
#warp{
@borderStyle();
}
/* 生成的 CSS */
#warp{
 border: 2px solid black;
 border-radius: 10%;
}
```



6. **变量运算**

- 加减法时 以第一个数据的单位为基准
- 乘除法时 注意单位一定要统一

```less
/*less*/
@fontSize:50px;
#warp{
font-size:@fontSize -20;//这里一定要有空格隔开，不然会认为是一个变量
}
/*css*/
#warp{
font-size:30px;
}
```

## 嵌套（&）

1. **&**

代表的上一层选择器的名字，可以理解为一个变量，只不过是动态的，最后的结果就是将&直接换成上一层选择器名称

```less
/* Less */
#header{
  &:after{
    content:"Less is more!";
  }
  .title{
    font-weight:bold;
  }
  &_content{//理解方式：直接把 & 替换成 #header
    margin:20px;
  }
}
/* 生成的 CSS */
#header::after{
  content:"Less is more!";
}
#header .title{ //嵌套了
  font-weight:bold;
}
#header_content{//没有嵌套！
    margin:20px;
}
```

！！！对于伪元素的hover调用，顺序为 

**选择器：hover：伪元素 {规则}**

```less
#warp{
	&:hover::after{
	...
	}
}
//css
#warp:hover::after{...}
```

2. **&的复用嵌套**

```less
// Multiple Parents `在这里插入代码片`
.nav {
    & + & {
        margin: 10px;
    }
    & & {
        padding: 10px;
    }
    && {
        width: 20px;
    }
    &>& {
        border: 1px solid red;
    }
    & , &bar {
        position: relative;
    }
}
//css
.nav + .nav {
  margin: 10px;
}
.nav .nav {
  padding: 10px;
}
.nav.nav {
  width: 20px;
}
.nav > .nav {
  border: 1px solid red;
}
.nav,
.navbar {
  position: relative;
}

```



3. **后置&巧用**

```less
// Change Selector Order
.header {
    .menu {
        border-radius: 5px;
        .no-border & {
            background-image: url("border.png");
        }
    }
}
//css
.header .menu {
  border-radius: 5px;
}
.no-border .header .menu {
  background-image: url("border.png");
}

```



## 混合（mixins）

- **无参数方法**

这里注意区别**变量和方法的区别**

变量以@开头来声明引入

而方法以 `.` 或者 `#` 作为方法前缀，也就是我们所说的**id选择器和类选择器都可以作为方法**，两种**调用或声明**方式，

1.  .name/#name
2.  .name()/#name()

两种方式作为声明有以下区别

name：声明下的样式规则会被编译到css中

name()：不会被编译输出

```less
/* Less */
.card { // 等价于 .card()
    background: #f6f6f6;
}
#wrap{
  .card;//等价于.card();
}
/* 生成的 CSS */
#wrap{
  background: #f6f6f6;
}
```



- **默认参数方法**

  - Less 可以使用默认参数，如果 没有传参数，那么将使用默认参数。

  - `@arguments` 犹如 JS 中的 `arguments` 指代的是 **全部参数**。

  - 传的参数中 **必须带着单位**。

```less
/* Less */
.border(@a:10px,@b:50px,@c:30px,@color:#000){
    border:solid 1px @color;
    box-shadow: @arguments;//指代的是 全部参数
}
#main{
    .border(0px,5px,30px,red);//必须带着单位
}
#wrap{
    .border(0px);
}
#content{
  .border;//等价于 .border()
}

/* 生成的 CSS */
#main{
    border:solid 1px red;
    box-shadow:0px,5px,30px,red;
}
#wrap{
    border:solid 1px #000;
    box-shadow: 0px 50px 30px #000;
}
#content{
    border:solid 1px #000;
    box-shadow: 10px 50px 30px #000;
}

```



- **方法的匹配模式**

  - 第一个参数 `left` 要会找到方法中匹配程度最高的，如果匹配程度相同，将全部选择，并存在着样式覆盖替换。

  - 如果匹配的参数 是变量，则将会匹配，如 `@_` 。

```less
/* Less */
.triangle(top,@width:20px,@color:#000){
    border-color:transparent  transparent @color transparent ;
}
.triangle(left,@width:20px,@color:#000){
    border-color:transparent  transparent  transparent @color;
}
.triangle(@_,@width:20px,@color:#000){
    border-style: solid;
    border-width: @width;
}
#main{
    .triangle(left, 50px, #999)
}
/* 生成的 CSS */
#main{
  border-color:transparent  transparent  transparent #999;
  border-style: solid;
  border-width: 50px;
}
```



- **方法命名空间**

命名空间使得方法更加规范

命名空间提供一个受保护的样式模板，使用嵌套的方式，定义了各个层级的样式

```less
.lessTree() {//括号是可选的
    background-color: rgb(200, 40, 149);//第一层级的内容

    .tree1(@first_width: 100px, @first_height: 100px) {
        width: @first_width;
        height: @first_height;

        .tree2(@border_w: 1px, @border_shape: solid, @border_color: #000, @border_r: 5%) {
            border: @border_w @border_shape @border_color;
            border-radius: @border_r;
        }
    }
}
```

上述模板实际上给我们提供了三个命名空间

.lessTree	.tree1	.tree2

根据层级关系，在引入全局时候，全局命名空间中有.lessTree，所以可以直接，后面的.tree1和2则不能直接调用

```less
//less
#warp{
	.lessTree;//.lessTree()
    //后续可以使用.tree1
}
//css
#warp{
    background-color: rgb(200, 40, 149);
}
```

调用.lessTree的同时引入了本层级的css语句规则，同时引入其命名空间，也就是说此时在#warp选择器的.lessTree后面可以正确调用.tree1

同理，引入了.tree1之后后面就可以正确调用.tree2



！！！注意，以>，空格等方式调用

```less
#warp{
	.lessTree > .tree1(800px,500px);
}
//css
#warp{
    width: 800px;
    height: 500px;
}
```

由最后解析结果可知，使用选择器来选择模板中的子类

1. 不会将前面所涉及到父类的css规则及命名空间引入

2. **会解析最后的目标子类中的规则，并引入最后子类的命名空间**



- **方法的条件筛选**

**when**	**and**	**not**	,

**要点**

- 比较运算有： > >= = =< <。

- = 代表的是等于
- 除去关键字 true 以外的值都被视为 false

```less
/* Less */
#card{
    
    // and 运算符 ，相当于 与运算 &&，必须条件全部符合才会执行
    .border(@width,@color,@style) when (@width>100px) and (@color=#999){
        border:@style @color @width;
    }

    // not 运算符，相当于 非运算 !，条件为 不符合才会执行
    .background(@color) when not (@color>=#222){
        background:@color;
    }

    // , 逗号分隔符：相当于 或运算 ||，只要有一个符合条件就会执行
    .font(@size:20px) when (@size>50px) , (@size<100px){
        font-size: @size;
    }
}
```



- **数量不定的参数**

可以使用... 实现不定数量的参数接收，犹如 ES6 的扩展运算符。

```less
/* Less */
.boxShadow(...){
    box-shadow: @arguments;
}
.textShadow(@a,...){
    text-shadow: @arguments;
}
#main{
    .boxShadow(1px,4px,30px,red);
    .textShadow(1px,4px,30px,red);
}

/* 生成后的 CSS */
#main{
  box-shadow: 1px 4px 30px red;
  text-shadow: 1px 4px 30px red;
}
```



- **!important**

直接在方法名后面添加`!important`，解析为css后即为每一条规则后面添加`!important`后缀

```less
/* Less */
.border{
    border: solid 1px red;
    margin: 50px;
}
#main{
    .border() !important;
}
/* 生成后的 CSS */
#main {
    border: solid 1px red !important;
    margin: 50px !important;
}
```



- **循环方法**

Less 并没有提供 for 循环功能，但这也难不倒 聪明的程序员，使用递归去实现。 下面是官网中的一个 Demo，模拟了生成栅格系统。

```less
/* Less */
.generate-columns(4);

.generate-columns(@n, @i: 1) when (@i =< @n) {
  .column-@{i} {
    width: (@i * 100% / @n);
  }
  .generate-columns(@n, (@i + 1));
}
/* 生成后的 CSS */
.column-1 {
  width: 25%;
}
.column-2 {
  width: 50%;
}
.column-3 {
  width: 75%;
}
.column-4 {
  width: 100%;
}
```



- **属性拼接方法**
- 可以据此实现同属性间的层级聚合

`+_` 代表的是 空格；`+` 代表的是 逗号。

- 逗号`+`

```less
/* Less */
.boxShadow() {
    box-shadow+: inset 0 0 10px #555;
}
.main {
  .boxShadow();
  box-shadow+: 0 0 20px black;
}
/* 生成后的 CSS */
.main {
  box-shadow: inset 0 0 10px #555, 0 0 20px black;
}
复制代码
```

- 空格`+_`

```less
/* Less */
.Animation() {
  transform+_: scale(2);
}
.main {
  .Animation();
  transform+_: rotate(15deg);
}

/* 生成的 CSS */
.main {
  transform: scale(2) rotate(15deg);
}
```

## 继承（extend）

- **extend关键字**

extend 是 Less 的一个伪类。它可**继承 所匹配声明中的全部样式**。

基于匹配声明式的继承是一大优势，但同时也可能带来代码重复性的问题，对比方法则是使用自己的声明。所以根据场景，考虑使用即可

**使用extend注意点**

- 选择器和扩展之间 是允许有空格的：pre:hover :extend(div pre).

- 可以有多个扩展: pre:hover:extend(div pre):extend(.bucket tr) - 注意这与 pre:hover:extend(div pre, .bucket tr)一样。

- **扩展必须在最后** 后面的代码是不可以的: pre:hover:extend(div pre):nth-child(odd)，改为pre:hover:nth-child(odd):extend(div pre)

- 如果一个规则集包含多个选择器，所有选择器都可以使用extend关键字。

```less
/* Less */
.animation{
    transition: all .3s ease-out;
    .hide{
      transform:scale(0);
    }
}
#main{
    &:extend(.animation);
}
#con{
    &:extend(.animation .hide);
}

/* 生成后的 CSS */
.animation,#main{
  transition: all .3s ease-out;
}
.animation .hide , #con{
    transform:scale(0);
}
```



- **all 全局搜索替换**

使用选择器匹配到的全部声明

```less
/* Less */
#main{
  width: 200px;
}
#main {
  &:after {
    content:"Less is good!";
  }
}
#wrap:extend(#main all) {}
//或者下面的方式
/*#wrap{
    &::extend(#main all)
}*/

/* 生成的 CSS */
#main,#wrap{
  width: 200px;
}
#main:after, #wrap:after {
    content: "Less is good!";
}
```



## 导入（import）

1. 导入 less 文件 可省略后缀

```less
import "main"; 
//等价于
import "main.less";
```

1. `@import` 的位置可随意放置

```less
#main{
  font-size:15px;
}
@import "style";
```



## reference

使用@import (reference)导入外部文件，但不会把导入的文件 编译到最终输出中，只引用。

只有使用extend或者mixins引入的样式规则会被编译进去

可以理解为less通过reference弱引入然后根据extend和mixins捕获相关的样式，并提取，类似TreeShaking

```less
/* Less */
@import (reference) "bootstrap.less"; //只做引入

#wrap:extend(.navbar all){}			//bootstrap.less中和.navbar相关的所有选择器的样式被添加到#warp选择器，并被编译到最终输出
```



## once

> @import语句的默认行为。这表明相同的文件只会被导入一次，而随后的导入文件的重复代码都不会解析。

```less
@import (once) "foo.less";
@import (once) "foo.less"; // this statement will be ignored
```



## multiple

> 使用@import (multiple)允许导入多个同名文件。

```less
/* Less */

// file: foo.less
.a {
  color: green;
}

// file: main.less
@import (multiple) "foo.less";
@import (multiple) "foo.less";

/* 生成后的 CSS */
.a {
  color: green;
}
.a {
  color: green;
}
```



## 函数

如果你想了解更多，可以去官网的[函数链接](https://link.juejin.cn/?target=http%3A%2F%2Flesscss.cn%2Ffunctions%2F)

### 类型判断

- **isnumber**

> 判断给定的值 是否 是一个数字。

```less
isnumber(#ff0);     // false
isnumber(blue);     // false
isnumber("string"); // false
isnumber(1234);     // true
isnumber(56px);     // true
isnumber(7.8%);     // true
isnumber(keyword);  // false
isnumber(url(...)); // false
复制代码
```

- **iscolor**

> 判断给定的值 是否 是一个颜色。

- **isurl**

> 判断给定的值 是否 是一个 url 。

### 颜色操作

- saturate

> 增加一定数值的颜色饱和度。

- lighten

> 增加一定数值的颜色亮度。

- darken

> 降低一定数值的颜色亮度。

- fade

> 给颜色设定一定数值的透明度。

- mix

> 根据比例混合两种颜色。

### 数学

- ceil

> 向上取整。

- floor

> 向下取整。

- percentage

> 将浮点数转换为百分比字符串。

- round

> 四舍五入。

- sqrt

> 计算一个数的平方根。

- abs

> 计算数字的绝对值，原样保持单位。

- pow

> 计算一个数的乘方。

## 其他
