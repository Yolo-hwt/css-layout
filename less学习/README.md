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

## 嵌套

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

## 混合

- **无参数方法**

这里注意区别变量和方法的区别

变量以@开头来声明引入

而方法以 `.` 或者 `#` 作为方法前缀，也就是我们所说的id选择器和类选择器都可以作为方法，两种调用方式，

1.  .name/#name
2.  .name()/#name()

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

`+_` 代表的是 空格；`+` 代表的是 逗号。

- 逗号

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

- 空格

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
