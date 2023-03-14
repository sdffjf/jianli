# resume-vue

### 自动简历生成

>  

## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8084
npm run dev

# build for production with minification
npm run build

# build for production and view the bundle analyzer report
npm run build --report
```

![](./demo3.gif)

### 项目实现简介:

> 有同学反馈项目实现的原理是什么，这里简要分析一下！！！

1、核心代码：
```
let len = _this.code.length;
// 每10ms 写入一次
var setIn = setInterval(function () {
    // 只显示作用
    _this.$refs.comShowStyle.writeStyleCode(_this.code.substring(0, n));
    // 渲染作用
    _this.$refs.comShowResume.responseStyleCode(_this.code.substring(0, n));
    n++;
    if (n >= len) {
        // 停止
        clearInterval(setIn);
    }
}, 10);
```
上面是核心代码，其实只是显示的一个假象而已哈。 this.code  是 css样式代码 ，可以看出分别是调用
子组件 `comShowStyle` 和 `comShowResume` 的方法 `writeStyleCode`和 `responseStyleCode`,
然后将 this.code  的 css字符串 每隔10ms截取一段 `_this.code.substring(0, n)` 传递给两个子组件。
接下来分别看看调用两个子组件分别作了什么。

2、ShowStyle 组件
```
<template>
    <div id="show-style">
        <div class="style-content" id="style_content">
            <pre class="style-code" v-html="styleMsg"></pre>
        </div>
    </div>
</template>
```
```
methods:{
    writeStyleCode: function (code) {
        this.styleMsg = code;
        // 每写一行 code ，滚动条滚动到最下面
        document.getElementById('style_content').scrollTop = document.getElementById('style_content').scrollHeight
    }
},
```
ShowStyle 组件拿到 code 字符串 后将数据传递给 vue的data数据，都知道vue的最大特色是数据的双向绑定，这样就会在
`<pre class="style-code" v-html="styleMsg"></pre>` 显示代码了。
没错，ShowStyle 组件 做的知识显示 code css 字符串 的代码而已。

3、ShowResume 组件
因为 code 字符串传递给 ShowStyle 组件的同时也传递一份给了 ShowResume 组件.
```
<div v-html="resStyleCode"></div>
```
```
methods:{
    responseStyleCode: function (code) {
        this.resStyleCode = "<style>"+code+"</style>"
    }
},
```
ShowResume 组件 也同样拿到 code字符串，不同的是以 `"<style>"+code+"</style>" ` 加个<style>标签
的形式赋值给 vue 的data数据 ，有了这个<style>标签，code字符串的 css样式就会作用于 ShowResume 组件
对应的 html 标签了。

就这样，一个组件负责加载 css字符串，同时把 css样式动态加载给另一个组件去渲染，这样就造成了边写css样式
一边渲染页面的假象，哈哈，可以clone 代码下来研究一下就理解啦。

#### [项目预览](https://zengtianshengz.github.io/blog/resume-vue/)

#### 关于 vue2.0 的项目实战可以看我之前写的一篇博文 ：[点此跳转](https://segmentfault.com/a/1190000008209734)

### 欢迎 star 和 issue
