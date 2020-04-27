# Vue



## 前端三要素：

1.结构层：HTML  



2.表现层：CSS

1. CSS层叠样式表是一门**标记语言**，并不是编程语言，不可以之定义变量，不具备语法支持，有了以下缺陷

   - 语法不够强大
   - 没有变量和样式复用机制，重复输出，维护困难

2. 所以需要使用==CSS预处理器==

   定义了一种新的语言，为CSS增加一些编程的特性

   常用的CSS预处理器

   - SASS 基于Ruby，上手难度大
   - **LESS** 基于NodeJS，使用简单

   - 

3. **行为层：JavaScript**

   >  JS框架

   - **jQuery**：简化DOM操作，缺点是DOM操作繁琐，但是影响前端性能
   - **Angular**：Google搜狗，有一群Java程序员开发。将MVC模式照搬到前端并增加了==模块化开发==的理念，对后端程序员友好
   - **React**：FackBook，一款高性能的JS前端框架：特点是提出新概念：==虚拟DOM==**，用于减少真实DOM 操作，在内存中模拟DOM 操作，**有效提升前端渲染效率**，缺点是使用复杂，需要学习JSX语言
   - **Vue**：一款渐进式JS框架，**特点是综合Angular（模块化）和React（虚拟DOM）的优点**
   - **Axios**：**前端通信框架**；因为Vue的边界很明确，为了DOM 操作，**不具备通信功能**，所以需要额外使用通信框架和服务器交互。也可以使用jQuery的Ajax

   > UI框架

   - Ant-Design : 阿里巴巴出品，基于React 的UI框架

   - ElementUI，iview,ice，饿了么出品，基于Vue的UI 框架，iview移动端，

     其中**ElementUI桌面端，组件齐全，四个质量较高的Vue UI 框架**

   - Bootstrap：Twitter出品，用于前端开发的开源工具包

   - AmazeUI：HTML5跨屏前端框架

   > JavaScript构建工具

   - Babel：JS编译工具，主要用于浏览器不支持的ES特性
   - WebPack：模块打包器，主要作用是打包，压缩，合并及按序加载

   > 前端的后端工具

   为了使前端人员方便开发后台应用，创建出NodeJS，但是架构不好，过于笨重，已在开发全新框架Deno

   - Express：NodeJS框架
   - Koa：Express 简化版
   - npm：项目综合管理工具，类似Maven
   - yarn：NPM的替代方案，类似于Maven和Gradle的关系

![1587456109665](../vue.js.assets/1587456109665.png)

## 概念介绍

- Vue是渐进式js框架，那么什么是==渐进式==：**主张最少**（对使用者的要求，必须使用的）

  通俗的话：“ 给你一个空屋，至于你需要什么自己一件件添，而不是那种家居家电全齐，自己不喜欢再一件件的扔了，甚至required 必须用且耗费空间的！ ”  vue可以作为一个**模板**，也可以作为一个**组件**加入

- Vue 被设计为可以==自底向上逐层应用==。Vue 的核心库==只关注视图层==，不仅易于上手，还便于与第三方库或既有项目整合。 
- soc 关注点分离：
  - 也就是说系统中的一个部分发生了变化，不会影响其他部分 
  -  即使需要改变，也能够清晰地识别出那些部分需要改变 
  -  如果需要扩展架构，将影响最小化，已经可以工作的每个部分都将继续工作。 

2. 1. 



## MVVM模式

> 什么是MVVM模式

- Model:模型层，表示javaScript对象
- view   ：视图层，这里表示DOM
- ViewModel ：连接视图和模型的中间件。Vue.js就是实现者
  - 能观测到数据的变化，并对视图进行实时更新
  - 能够监听视图层的变化，通知模型层进行改变

> Vue作为VM的优点

- 轻量级
- 易上手
- 吸取了Angular（模块化）和React（虚拟DOM）的优点
- 开源，热门

> 前后端分离

由下面的入门得到的结论：view层得到的是ViewModel的数据而不是Model的，由ViewModel负责和Model沟通。这完全解耦了View和Model。这是前后端分离至关重要的一步

# 入门

> 安装插件和导包

```js
<script src="https://cdn.staticfile.org/vue/2.2.2/vue.min.js"></script>
```

> view层

```html
<!--这个就是视图层，显示数据，这个数据从Model层经过ViewModel来到View，ViewModel是至关重要的一步-->
<div id="app">
    <!--    这里显示的就是model中给的数据，实时更新，
        这就是虚拟DOM：不操作DOM，运行和编译同时进行，直接更新数据，不需要刷新页面-->
    {{message}}
</div>
<div id="app2">
    {{hello}}
</div>
```

> model层

```js
/*vue就是模型层，提供数据*/
    var vue = new Vue({
        //绑定元素
        el: "#app",
        //数据交给前端控制
        data:{
            message:"hello Vue"
        }
    })

    var vue = new Vue({
        el:"#app2",
        data:{
            hello:"hi"
        }
    })
```

> ViewModel层：双向绑定

model层改变数据，视图层立马改变

![1587997752742](vue.js.assets/1587997752742.png)

![1587998262803](vue.js.assets/1587998262803.png)

> 虚拟DOM 的实现

View显示数据不用通过操作DOM，而是来自ViewModel的执行和编译同步，实时更新Model传来的数据





## 指令绑定v-bind

```html
<div id="app2">
<!--    绑定在元素上-->
    <span v-bind:title="hello">鼠标悬停在上面会显示信息</span>
     <p>{{hello}}</p>
</div>
```

![1587999552281](vue.js.assets/1587999552281.png)

## v-if

```html
<div id="app">
<!--    vue的判断-->
    <h1 v-if="message">yes</h1>
    <h1 v-else>no</h1>
</div>
```

```js
var vue = new Vue({
        el:'#app',
        data:{
            ok:true
        }
    })
```

结果：

​	yes

## v-for

```html
<div id="app">
    parents:
{{name}}:
<!--类似于foreach-->
    <li v-for="list in lists">
        children:
        {{list.message}}
        {{list.message1}}-
    </li>
</div>
<script>
```

```js
var vue = new Vue({
        el:'#app',
        data:{
            name:'yzy',
            //数组中有多个键值对
            lists:[
                {message:"YY"},
                {message1:"YZY"}
                ]
        }
    });
```

结果：

​		parents: yzy: 

​        children:        YY        -    

​        children:                YZY-    