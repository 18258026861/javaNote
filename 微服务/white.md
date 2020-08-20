# Vue
准备Vue的环境
1.node.js
2.安装脚手架`cnpm  install -g @vue/cli`
3.生成项目模板,进入项目目录，命令行：`vue init webpack 项目名`,一路yes
4.执行项目`npm run dev`


其实用idea也可以创建项目而且更简单

## 项目目录
![20200814091022](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200814091022.png)
![20200814091051](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200814091051.png)

### index.html
上面的目录显示**整个项目只有一个html文件**。我们编写的所有页面都会展示在这个html页面中的div中

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <title>y</title>
  </head>
  <body>
    <div id="app"></div>
    <!--构建的文件会被自动注入，编写的其他内容都会在这个div中展示-->
    <!--整个项目只有这一个html文件，是个单页面应用，当我们打开这个应用时，表面上有很多页面，其实都在这个div中-->
    <!-- built files will be auto injected -->
  </body>
</html>
```

### App.vue
在src目录的router下，是项目的**根组件**，其他的组件都包含在这个组件中
- touter-view:**路由视图**，配置文件是router文件夹的index.js，将当前路由值指向的内容显示在容器中，具体解释可以看下面注释
- export default:将这个组件**整体导出**并设置它的属性，可以使用**import来导入这个组件**
```html
<template>
  <div id="app">
    <img src="./assets/logo.png">
    <!--最关键的！是一个路由视图，/的意思是将当前路由（URL的值）指向的内容显示在这个容器中
                当其他的组件拥有自己的路由（URL值在index.js中设置），也只是表面上的单独页面，其实在这个根目录上
                自己的理解：当其他组件拥有路由，即扎根于这个路由视图上，那么相当于其他组件是这个根组件的分支-->
    <router-view/>
  </div>
</template>

<script>
/* ES6的语法，将这个组件整体导出并命名为App，就可以使用import来导入 这个组件了 */
export default {
  /* 组件的相关信息 */
  name: 'App'
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>


```

### main.js入口文件
- 实例化Vue,挂靠到主页面指定的dom节点上，**将组件加工成vue实例和标签**
- 设置插件和静态资源（如axios插件）

**具体解释**：组件整体导出export default，然后在这个js文件中引用，并创建一个vue实例，该实例包含了指定的组件，并且可以使用<App/>标签代替，也可以挂靠到DOM节点上
```js
// The Vue build version to load with the `import` command
// 使用“import”命令加载的Vue构建版本
// (runtime-only or standalone) has been set in webpack.base.conf with an alias.
// (仅运行时或独立运行时)在webpack.base.conf中设置了一个别名。

/* 这里引用了如我上述的被export defalut整体导出的组件 */
import Vue from 'vue'
import App from './App'
import router from './router'

/* 作用是阻止vue 在启动时生成生产提示。 */
Vue.config.productionTip = false

/* eslint-disable no-new */
/* vue实例，以一个id=app的dom元素作为载体
*   router代表该对象包含Vue Router，并使用项目中定义的路由
*   components代表该对象包含的组件
*   templates代表用一个字符标签作为Vue的实例使用 */
new Vue({
  el: '#app',
  router,
  components: { App },
  template: '<App/>'
})
```
#### 设置反向代理
在main.js中设置
设置axios实例，并设置默认后端接口，所有前端请求就会发送到该接口。
全局注册axios，其他组件可以使用this.$axios来使用该axios
```javascript
/*设置反向代理,前端默认发送到后端接口 'https://localhost:8843/api'*/
var axios = require('axios')
axios.defaults.baseURI = 'https://localhost:8843/api'
/*全局注册,之后就可以在其他组件中使用this.$axios 来发送数据*/
Vue.prototype.$axios = axios
/* 作用是阻止vue 在启动时生成生产提示。 */
Vue.config.productionTip = false
```
设置后需要安装axios模块`cnpm install --save axios `







### 几个文件之间的关系
- index.html: 显示的主页面
- App.vue: 根组件
- main.js：入口文件，应用到index.html中


**流程**：main.js实例化Vue，并挂载到index.html的app上，而实例化Vue中包含了App.vue组件，即通过main.js实例化Vue，成功将组件挂靠到了index.html上面

![](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/html上面.png)





## 前端开发
### 登录页面
前面说了**整个项目只有一个html**，所以想要新建新页面，就创建vue文件
**关键字介绍**：
- **template代表login网页的模板**
- export default将这个模板导出，
  - name为模板名称
  - **data**里面存放数据
    - return代表模板返回的数据
    - loginForm是个对象，内容有帐号和密码
  - **methods**存放方法
    - 表示方法和ajax类似，**post代表请求方式**，参数为接口和数据
    - **then代表获得的响应**（可能成功，可能失败），参数为response

**流程介绍**：将login页面写成一个模板并导出，当其他页面引用时，v-model绑定给data中的变量，点击登录，触发login方法，将帐号密码请求到/login接口，并获得相应，如果登录成功，跳转到index页面

```html
<!--定义login页面的模板-->
<template>
  <div>
    <!--v-model双向绑定，将输入的值绑定到model-->
    用户名：<input type="text" v-model="username" placeholder="请输入帐号">
    <br>
    密码: <input type="password" v-model="password" placeholder="请输入密码">
    <br>
    <button v-on:click="login" >登录</button>
  </div>
</template>

<script>
  /*提供一个出口用于应用,return代表输出里面的内容*/
export default {
  name: 'Login',
  data () {
    return {
      loginForm: {username: '',
        password: '',
        result: ''
      }
    }
  },
  methods: {
    login (){
      this.$axios
        .post('/login',{
          username: this.username,
          password: this.password
        })
        /*//和ajax差不多 get(获取的接口)    ，then（参数）获得响应，并进行操作（输出响应的数据）*/
      .then(function (response){
        /*获取获得的响应,如果成功,就返回到index页面*/
          if(response.data().code === 200){
            this.$router.replace({path: '/index'})
          }
      })
      .catch(function (response) {
            
      })
    }
  }
}
</script>
```


### 配置页面路径（路由视图）
进入src/router文件夹，index.js

在App.vue中所说，router是一个路由视图，其他组件的路由（URL）设置要在该容器上设置，那么就要**引入其他组件**，然后**设置路径**
下列代码：设置了login的路径和appIndex的路径
```js
import Vue from 'vue'
import Router from 'vue-router'
import HelloWorld from '@/components/HelloWorld'
/*添加刚才新增的两个组件,appIndex和Login*/
import appIndex from "../components/home/appIndex";
import Login from "../components/Login";

Vue.use(Router)

export default new Router({
  routes: [
    {
      path: '/',
      name: 'HelloWorld',
      component: HelloWorld
    },
    /*设置组件的路由URL,以便于可以跳转到指定网页*/
    {
      path: '/login',
      name: 'Login',
      component: Login
    },
    {
      path: 'index',
      name: "appIndex",
      component: appIndex
    }
  ]
})
```

### 跨域支持
`conf\index.js`（项目配置）中的proxyTable

**当匹配到/api就自动加上后端接口的地址,开启跨域,并将/api去掉**

举例:当我请求/api/login登录就扣,我就加上后端接口....8443/api/login,再将/api去掉,变成8443/login,成功访问到了后端具体接口
                  
```javascript
proxyTable: {
      '/api' : {
        target : 'https:localhost:8443',
        changeOrigin: true,
        pathRewrite: {
          '^/api':  ''
        }
      }
    },
```

### 访问登录页面
接下来就可以访问页面le,`npm run dev`
主页：http://localhost:8080/#/

登录页面：localhost；8080/#/login ,#是当页面应用的写法，后面的是页面路径

![20200814160617](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200814160617.png)

描述过程：基础流程在几个基本文件的关系中已讲，来讲一下登录页面的流程：

1.编写登录组件，并导出

2.在路由中设置登录组件的URL，此时就绑定在根组件上了
![20200814161951](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200814161951.png)
3.因为根组件已经挂靠在主页上，所以登录组件也绑定上了，主页地址为8080/#，登录组件的URL为/login，那么登录页面及在主页后加/login ：8080/#/login



# springboot
## 后端配置
### 配置数据库
1.数据库创建

```sql
CREATE TABLE `user` (
  `id` int unsigned NOT NULL AUTO_INCREMENT,
  `username` varchar(255) DEFAULT NULL,
  `password` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8
```


2.springboot的数据库配置

在application文件中设置数据库

```properties
#配置数据库
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/white?characterEncoding = UTF-8
spring.datasource.username=root
spring.datasource.password=yzy665128
```

3.根据数据库创建实体类

包含数据库的字段并有get，set方法
```java
public class User {

    private int id;
    private String username;
    private String password;
```
### mybatis过程
1.添加mybatis依赖

2.增加Dao包用于存放业务接口，Mapper注解和业务接口

3.在resource资源目录下创建mapper包用于存放mapper映射文件
namespace为对应的业务接口文件

4.在application配置文件中配置mybatis的别名包位置（数据库实体类）和mapper包位置

5.service调用业务接口，@Service注解+@Autowired 业务接口



## 登录业务实现
1.创建登录业务pojo文件
```java
@Mapper
public interface LoginDao {
     User findByUsername(String username);
     User findByUsernameAndPassword(String username,String password);
}
```
2.mapper文件下创建业务映射文件





# 前后端结合流程
前后段通**过Restful API传递Json字符串**，后端不涉及页面。前端使用前端的服务器Nginx，后端使用后端服务器Tomcat。
前端可以通过前端服务器将请求转发给后端（反向代理），能够实时观察结果，不需要知道后端实现，只要知道接口提供的功能。
- 反向代理：用户访问想要的内容，前端服务器向后端服务器索取到想要的内容返回给用户。这样有利于**保护服务器，不暴露真实地址**
- 正向代理：用于通过一个代理服务器浏览到想要浏览的内容
# 遇到的问题
## 安装
### 1.无法安装脚手架
重装node2
### 2.无法下载模板，联网失败
下载离线包https://github.com/vuejs-templates/webpack，将包解压放在C:\Users\Barcelona\.vue-templates下并改名成webpack，执行`vue init webpack 项目名 --offline`
