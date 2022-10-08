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

1. 值变量

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

