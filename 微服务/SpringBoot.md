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



# 

# 自动装配

## 依赖

#### pom.xml

pom.xml的**父类** spring-boot-starter-parent



> 启动器
>
> > spring-boot-starter-XXX:从dependencies中自动导入环境依赖

**例如**-web：**自动导入web环境的所有依赖**

点击去就能发现**很多依赖包**：spring-web，spring-webmvc，tomcat，json

```xml
 <dependency>
<!--          启动器，web这个依赖包含了SpringMvc,自动装配，使用tomcat为默认容器-->
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
```





####  spring-boot-starter-parent

>  资源过滤,maven插件

```
<resources>
      <resource>
        <filtering>true</filtering>
        <directory>${basedir}/src/main/resources</directory>
        <includes>
          <include>**/application*.yml</include>
          <include>**/application*.yaml</include>
          <include>**/application*.properties</include>
        </includes>
      </resource>
      <resource>
        <directory>${basedir}/src/main/resources</directory>
        <excludes>
          <exclude>**/application*.yml</exclude>
          <exclude>**/application*.yaml</exclude>
          <exclude>**/application*.properties</exclude>
        </excludes>
      </resource>
    </resources>
```

#### dependenices

>所有功能场景变成启动器

spring-boot-dependencies 包下，已经指定好了那个版本，依赖时就不需要指定了

![1588217280640](SpringBoot.assets/1588217280640.png)

这些就是**启动器**

如果你想用哪个环境，就启用对应的启动器就行了

> 自带依赖

![1588314202626](SpringBoot.assets/1588314202626.png)

pom.xml的web包就是从dependencies中取出来



## 主程序

```java
//这个类就是一个Component
@SpringBootApplication
//@SpringBootApplication标志这个类是一个springboot的应用
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }

}
```



### 注解



#### @主配置类：springBootApplication：

==作用==：将该包及其所有子包纳入spring容器,**这就是为什么要将业务放到主程序同级目录下的原因**

```java
//元注解，用来修饰当前注解，就像public类的修饰词，无实际功能
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
// springboot的配置，说明这是一个配置文件类，它会被@ComponentScan扫描到
@SpringBootConfiguration
// 开启自动装配
@EnableAutoConfiguration
//扫描包（filters过滤一些东西）
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {
```



##### @SpringBootConfiguration：主程序为配置类

```
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
// 配置类
@Configuration
public @interface SpringBootConfiguration {
```







#### @EnableAutoConfiguration

==作用==：自动注册包和引入需要的第三方jar包

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
//获取所在包进行注册
@AutoConfigurationPackage
// 将第三方jar引入
@Import(AutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {
```



##### @AutoConfigurationPackage：所在包注册

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
// 对源数据所在包下组件进行注册
@Import(AutoConfigurationPackages.Registrar.class)
public @interface AutoConfigurationPackage {
```



###### @AutoConfigurationPackages.Registrar

==作用==：打包源数据获取包名调用register注册

**register**方法： 注册包

```java
static class Registrar implements ImportBeanDefinitionRegistrar, DeterminableImports {

		@Override
		public void registerBeanDefinitions(AnnotationMetadata metadata, BeanDefinitionRegistry registry) {
            // 	new PackageImport(metadata).getPackageName())将源数据打包，并获得其包名
			register(registry, new PackageImport(metadata).getPackageName());
		}

		@Override
		public Set<Object> determineImports(AnnotationMetadata metadata) {
			return Collections.singleton(new PackageImport(metadata));
		}

	}
```

**包名**就是这个，主程序所在的包

![1588322355314](SpringBoot.assets/1588322355314.png)

##### @Import：三方jar包

List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes);

进入getCandidateConfigurations

> getCandidateConfigurations:

```java
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
    //  这边获取所有配置  getSpringFactoriesLoaderFactoryClass()方法就在下面
		List<String> configurations = SpringFactoriesLoader.loadFactoryNames(getSpringFactoriesLoaderFactoryClass(),
				getBeanClassLoader());
    //  如果为空，输出语句，META-INF/spring.factories
		Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you "
				+ "are using a custom packaging, make sure that file is correct.");
		return configurations;
	}

//   EnableAutoConfiguration返回的就是主程序的包，所以获取的是springboot的所有配置
	protected Class<?> getSpringFactoriesLoaderFactoryClass() {
		return EnableAutoConfiguration.class;
	}

```

###### loadFactoryNames

![](SpringBoot.assets/QQ图片20200501175429.png)

> spring.factories

所在地址：

![1588255539786](SpringBoot.assets/1588255539786.png)

里面储存的都是配置类的路径，当你需要用时，就会通过路径调用配置类（比如视图解析器）

> 

#### 导图

![1588328045273](SpringBoot.assets/1588328045273.png)

---



### 主启动程序

#### springApplication

**这个类主要做了以下四件事情：**

1、推断应用的类型是普通的项目还是Web项目

2、查找并加载所有可用初始化器 ， 设置到initializers属性中

3、找出所有的应用程序监听器，设置到listeners属性中

4、推断并设置main方法的定义类，找到运行的主类

查看构造器：

```java
public SpringApplication(ResourceLoader resourceLoader, Class<?>... primarySources) {
    
    //	 加载资源
		this.resourceLoader = resourceLoader;
    //  primarySource不能为空
		Assert.notNull(primarySources, "PrimarySources must not be null");
    //  遍历primartSource
		this.primarySources = new LinkedHashSet<>(Arrays.asList(primarySources));
    //   推测web应用类型，并赋值到属性webApplicationType
		this.webApplicationType = WebApplicationType.deduceFromClasspath();
    //   监听初始化
		setInitializers((Collection) getSpringFactoriesInstances(ApplicationContextInitializer.class));
		setListeners((Collection) getSpringFactoriesInstances(ApplicationListener.class));
		this.mainApplicationClass = deduceMainApplicationClass();
	}
```

---



#### run 流程

![1588311036777](SpringBoot.assets/1588311036777.png)

==图片对应的代码==

```java
public ConfigurableApplicationContext run(String... args) {
       // 计时器
        StopWatch stopWatch = new StopWatch();
        //  计时器启动
        stopWatch.start();
//        初始化上下文，异常报告集合
        ConfigurableApplicationContext context = null;
        Collection<SpringBootExceptionReporter> exceptionReporters = new ArrayList<>();
//        strep1 系统属性设置
        configureHeadlessProperty();
        //  step2 创建所有spring监听器，底层采用factoriesInstances，通过类加载器获取spring.factories文件，进而反射得到class对象
        SpringApplicationRunListeners listeners = getRunListeners(args);
//        启动所有监听器
        listeners.starting();
        try {
//            step4  装配环境参数（如service.port=8082）
            ApplicationArguments applicationArguments = new DefaultApplicationArguments(args);
//             用监听器和环境参数创建配置环境
            ConfigurableEnvironment environment = prepareEnvironment(listeners, applicationArguments);
            configureIgnoreBeanInfo(environment);
//            step5 打印banner和环境
            Banner printedBanner = printBanner(environment);
//            step 6创建应用上下文
            context = createApplicationContext();
//            异常报告
            exceptionReporters = getSpringFactoriesInstances(SpringBootExceptionReporter.class,
                    new Class[] { ConfigurableApplicationContext.class }, context);
//            应用上下文准备完毕
            prepareContext(context, environment, listeners, applicationArguments, printedBanner);
//            step9 上下文刷新：bean工厂加载，通过工厂生产Bean，刷新生命周期
            refreshContext(context);
//              上下文后置结束处理（上下文和环境参数）
            afterRefresh(context, applicationArguments);
//            计时器结束
            stopWatch.stop();
            if (this.logStartupInfo) {
                new StartupInfoLogger(this.mainApplicationClass).logStarted(getApplicationLog(), stopWatch);
            }
//            上下文的监听完成
            listeners.started(context);
//             step12 执行所有runner运行器
            callRunners(context, applicationArguments);
        }catch (Throwable ex) {
            handleRunFailure(context, ex, exceptionReporters, listeners);
            throw new IllegalStateException(ex);
        }

        try {
//          step13 发布应用上下文
            listeners.running(context);
        }
        catch (Throwable ex) {
            handleRunFailure(context, ex, exceptionReporters, null);
            throw new IllegalStateException(ex);
        }
//        step13 就绪并返回
        return context;
    }
```



# 配置文件

springBoot的配置文件有两种

![1588341092753](SpringBoot.assets/1588341092753.png)

## properties

语法结构   key = value

```properties
service.port = 8081
```

如果使用properties和类绑定

```java
// 加载指定配置文件
@PropertySource(value = "classpath:student.properties")
public class Student {

//    使用properties文件设置属性
    @Value("${student.name}")
     private String name;
```



## yaml

>  语法结构  : key : 空格 value

空格的要求非常严格，**垂直对齐**，属性需要**缩进**

```yml
service:
   port: 8081
```

>  对象

```yaml
student:
  name: YY
  age: 22
  
#行内写法
student1: {name: YY,age: 22}
```

> 数组

```
#数组
pets:
  - cat
  - dog
  - pig

pets1: [cat,dog,pig]
```

### 属性赋值

 pojo

```java
//前提，这个类是组件
@Component
//将配置文件的每一个属性映射到这个组件中，将本类的所有属性和配置文件中的相关配置（student下的所有属性）对应绑定
@ConfigurationProperties(prefix = "student")
public class Student {
    private String name;
    private int age;
```

test

```java
@SpringBootTest
class DemoApplicationTests {
    @Autowired
    Student student;

    @Test
    void contextLoads() {
        System.out.println(student.getAge());
    }

}
```



### 松散绑定

数据库字段名last-name  可以和 驼峰命名法lastName 自动绑定

```
private String LastName;
```



### JSR303数据校验

**需要先开启@validate数据校验**

![1588342848385](SpringBoot.assets/1588342848385.png)



```
//数据校验
@Validated
public class Student {

    @Email(message = "📪error")
    private String name;
```



## 多环境配

### 优先级

![1588344302070](SpringBoot.assets/1588344302070.png)

### 指定环境

> 多文件

![1588344991080](SpringBoot.assets/1588344991080.png)

```properties
#springboot的多环境配置，可以选择激活哪一个
spring.profiles.active=work
```

> 单文件

```yaml
#springboot的多环境配置，可以选择激活哪一个
spring:
  profiles:
    active: test
#使用---分割
---
server:
  port: 8083
spring:
  profiles: test

---
server:
  port: 8082
spring:
  profiles: work
---
server:
  port: 8084
spring:
  profiles: up

```



## 错误

1. 把server 写成service 导致端口修改没有生效
2. 单文件配置多环境时，文件名必须是



## 配置类

**自动配置类**为XXXAutoConfiguration

**配置类名**为XXProperties

> EnableConfigurationProperties

==作用==：**自动配置类**会将**配置类**所在的包注册，并设默认值

![1588346645924](SpringBoot.assets/1588346645924.png)



> ConfigurationProperties

**配置类**和配置文件绑定

![1588347315440](SpringBoot.assets/1588347315440.png)

> 引入配置类的条件

当所有条件成立，**自动配置类**才会**生效**，才能将配置类**注册**进IOC容器

![](SpringBoot.assets/QQ图片20200501201143.png)



> 手动配置参数

![1588335522544](SpringBoot.assets/1588335522544.png)

![1588335764204](SpringBoot.assets/1588335764204.png)

prefix对应的就是配置类名，然后设置它的属性

```properties
#设置配置类的属性,配置类的Configuration和配置文件绑定，那么prefix对应的就是类名，就可以设置类名的属性
spring.data.web.page = 10
```



### 查看生效的配置类

```yaml
debug: true
```

positive match：显示生效的配置类

nagative match：显示未生效的配置类