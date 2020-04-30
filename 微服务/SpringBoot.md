# SpringBoot

>  学习流程

![1588145129189](SpringBoot.assets/1588145129189.png)

> 什么事SpringBoot

SpringBoot基于Spring开发，并不是替代Spring，而是和Spring紧密结合用于提升Spring体验。

SpringBoot **约定大于配置**，默认进行了很多配置，只需要很少的配置就可以开发。

> 优点

- Spring开发更加入门
- 开箱即用，简化配置
- 内嵌容器简化Web项目
- 没有XML配置和冗余代码



## 微服务

> 什么是微服务

 微服务是一种**架构风格**。 是一种将一个单一应用程序开发为一组小型服务的方法，每个服务运行在自己的进程中，服务间通信采用轻量级通信机制(通常用HTTP资源API)。 

业务拆分成一个一个的服务，彻底去掉耦合，每一个微服务提供单个业务功能，一个服务只做一件事。 小服务之间用http或RPC方式互通

> 单体应用框架

就是SSM，全部放在一个服务器上，打包为一个war包。

**好处**：

- 易于开发和测试，部署十分方便。需要拓展时，可以复制多个war包到多个服务器，做个负载均衡

**坏处**：

- 我要修改一个小小的地方也要停掉所有服务，重新打包成一个war包

> 微服务架构

把功能独立出来，业务由一个或多个功能组成。主要多个业务就赋值多个功能而不是war包

**好处**：

- 节省了资源，减少代码冗余
- 每个功能都是独立的，便于维护

但是，这种庞大的系统架构给部署和运维带来了很大难度。，Spring给了一套完整的微服务：

- 构建独立功能用**SpringBoot** 快速构建
- 大型**分布式网路服务**的调用使用**Spring cloud** 来实现分布式
- **中间件**有**spring cloud data flow**
- 

  http://blog.cuicc.com/blog/2015/07/22/microservices/# ![img](SpringBoot.assets/sketch.png)  

# 第一个springboot项目

![1588213837946](SpringBoot.assets/1588213837946.png)

**选择web**

![1588213859198](SpringBoot.assets/1588213859198.png)

**完成！**

![1588213890998](SpringBoot.assets/1588213890998.png)



> 建包要在主程序的同级目录下

![1588213967068](SpringBoot.assets/1588213967068.png)

> controller

```java
@RestController
public class Contreoller {

    @RequestMapping("/YY")
    public String data(){

        return "YY";
    }
}
```

> 运行效果

![1588214765411](SpringBoot.assets/1588214765411.png)

> 更改项目端口号

![](SpringBoot.assets/QQ图片20200430110530.png)

> banner

资源目录下创建banner.txt



## 打包

> package

![1588214808366](SpringBoot.assets/1588214808366.png)

> 位置

在target目录下

![1588214848702](SpringBoot.assets/1588214848702.png)

> powershell

在jar的目录下，**按住shift和右键**，打开powershell窗口

输入java -jar .\项目名

![1588215197111](SpringBoot.assets/1588215197111.png)

服务启动了,关闭idea的tomcat，再次打开 http://localhost:8080/YY ，还是能访问！

关闭powershell窗口后就无法访问了

> 微服务

这就是微服务，把服务变成一个个块，不依赖idea也能打开，内置了tomcat



# 原理

## 自动装配

### 依赖

parent一栏中点进去

spring-boot-starter-parent----》spring-boot-dependencies

这个pom文件里全部都是依赖

#### 启动器

```xml
 <dependency>
<!--          启动器，web这个依赖包含了SpringMvc,自动装配，使用tomcat为默认容器-->
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
```

> spring-boot-starter:自动导入环境依赖

**例如**-web：**自动导入web环境的所有依赖**

点击去就能发现**很多依赖包**：spring-web，spring-webmvc，tomcat，json



> 所有功能场景变成启动器

spring-boot-dependencies 包下

![1588217280640](SpringBoot.assets/1588217280640.png)

这些就是**启动器**

如果你想用哪个环境，就启用对应的启动器就行了



### 主程序

```java

@SpringBootConfiguration		//代表这是一个springBoot
//->
@Component   //代表这是一个组件

//自动配置类
@EnableAutoConfiguration
//->
@AutoConfigurationPackage   //自动配置包
```



springboot启动的时候，从类路径下/META-INF/spring.factories获取指定的值

![1588255539786](SpringBoot.assets/1588255539786.png)