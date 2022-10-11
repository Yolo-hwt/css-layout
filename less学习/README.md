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

