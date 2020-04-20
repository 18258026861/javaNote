Spring

# spring介绍

是一个轻量级的***控制反转（IOC），面向切面编程（AOP）的框架（容器）***，AOP的主要作用就是***支持事务的处理***

- springboot 是一个快速开发的脚手架
-- 基于springboot可以快速开发单个微服务
-- 约定大于配置

- springcloud
-- springcloud是基于springboot实现的

现在多数公司在使用springboot进行快速开发，学习**springboot**需要先学习**spring**和**springMVC**

spring理念：整合现有技术，是个大杂烩。弊端就是配置十分繁琐

**IOC：控制反转**

什么叫控制反转，本来程序的主动权在程序员手里，客户需要什么，程序员调用什么。控制反转就是把控制权转交到客户手里，客户需要什么就自己选择，不需要程序员调用

---
 XML前缀

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:util="http://www.springframework.org/schema/util"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util.xsd">
    
</beans>

```

---

# IOC

# 1.创建bean的三种方式



### 1.无参构造创建（主要）

  spring的配置文件使用bean标签且只有id和class属性，没有其他属性和标签，

 - 如果没有默认构造函数，就不能创建

   ```xml
   <bean id="accountService" class="service.impl.AccountServiceImpl"/>
   ```

### 2.jar包

- 使用jar包中的方法创建对象（某个类中的方法创建对象），并存入spring容器

    ```xml
    <bean id="instanceFactory"  class="com.gx.InstanceFactory"/>
    <bean id="accountService"factory-bean="instancefactory" factory-method="saveAccount"/>
    ```

    

### 3.静态方法创建对象

-  使用工厂中的静态方法创建对象（某个类中的静态方法），并存入spring容器
          static不需要实例化没，所以可以直接合并成一句话
      
- ```xml
     <bean id="staticFactory" class="com.gx.staticFactory" factory-method="saveAccount"></bean>
     ```

     

---

## 2. bean对象的作用范围

- bean的scope属性： 用于指定bean的作用范围
     - 取值：singleton：单例的（默认值）
         - prototype：多例的
         - request:作用web请求的请求范围
         - session:作用于web应用的会话范围
         - blobal-session;作用于全局会话（集群）范围，不是集群范围就是session
         
         ```xml
         <bean id="accountService" 
               class="service.impl.AccountServiceImpl"
               scope="prototype"/>
         ```
         
         

---

## 3 bean对象的生命周期

- 单例对象：单例对象的生命周期跟随容器
     - 出生:但容器创建时，对象就出生
     - 存活：容器活着对象就活着
     - 死亡：容器销毁，对象死亡
- 多例对象：
     -  出生：当我们使用对象时spring框架为我们创建
     - 存活：单对象在使用中就存活
     - 死亡：单对象长时间不用，且没有别的对象应用，就被被垃圾回收

*        <bean id="accountService" class="service.impl.AccountServiceImpl" scope="prototype" init-method="init" destroy-method="destroy"/>

---

# 2.spring依赖注入

依赖注入DI：Dependency Injection:降低程序的耦合 
- 能注入的数据由三种：
     - 基本类型和string
     - 其他bean类型（在配置文件中活着注解配置的bean）
     - 复杂类型、集合类型
- 注入的方式三种：
     - 构造函数提供
     - 使用set方法
     - 使用注解

## 1.构造函数注入

- ***construct-arg*** ：因为默认构造已经不见了，所以要使用标签：construct-arg
     - 标签中的属性：
         - type：指定要注入的数据类型
         - index：索引位置，指定要注入的数据，从0开始
         -（常用）name：给指定名称赋值
         - value:给基本类型和String类型数据
         - ref:给其他类型数据，在spring容器中出现过的
- ***优势***：创建bean对象时，必须注入数据，否赵不能创建
- ***劣势***：改变了bean实例化，当我们用不到某些数据也必须赋值
          所以除了非用不可之外，一般使用set方法

*       ```xml
        <bean id="accountService" class="service.impl.AccountServiceImpl">
        <constructor-arg name="name" value="YY"></constructor-arg>
        <constructor-arg name="age" value="18"></constructor-arg>
        <constructor-arg name="birthday" ref="date"></constructor-arg>
        </bean>
        <bean id="date" class="java.util.Date" ></bean> 
        ```
        
        

---

## 2.set方法注入

- 标签： property，出现在bean内部
     - 标签中的属性：
        - name：给指定名称赋值,这边的name不是变量名，而是set的名称
        - value:给基本类型和String类型数据
        - ref:给其他类型数据，在spring容器中出现过的类里没有默认构造函数不能使用
- **优势**；创建对象没有明确限制，可以直接使用默认构造方法
- **弊端**：没有set方法就不能注入

*       ```xml
        <bean id="accountService2" class="service.impl.AccountServiceImpl2">
        <property name="name" value="YY"/>
        <property name="age" value="18"/>
        <property name="birthday" ref="date"/>
        </bean>
        <bean id="date" class="java.util.Date" ></bean>        
        ```



复杂类型、集合类型的注入

- 用于给List结构注入的标签：

  - list array set

- 用于Map结构集合注入的标签：

  - map props

- 结构相同，可以互换，所以只要记两组就行了

  

```xml
<bean id="accountService3" class="service.impl.AccountServiceImpl3">
     <property name="strings">
         <array>
             <value>YY</value>
             <value>YZY</value>
         </array>
     </property>
     <property name="set">
         <set>
             <value>YY</value>
             <value>YZY</value>
         </set>
     </property>
     <property name="map">

         <map>
             <entry key="A" value="B"></entry>
         </map>

​     </property>
​     <property name="list">
​         <array>
​             <value>YY</value>
​             <value>YZY</value>
​         </array>
​     </property>
​     <property name="properties">
​         <props>
​             <prop key="A" >a</prop>
​             <prop key="B" >b</prop>
​         </props>
​     </property>
 </bean>
 <bean id="person" class="entity.Person" autowire="byType">
​     
​    </bean>
​    <bean id="dog" class="entity.Dog"/>
​    <bean id="cat" class="entity.Cat"/>
```



## 3.注解注入（放到注解里讲）

**不需要setter方法**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans
        xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:p="http://www.springframework.org/schema/p"
        xmlns:context="http://www.springframework.org/schema/context"
        xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context-3.0.xsd
    ">
    <!--支持注解模式-->
    <context:annotation-config></context:annotation-config>
    
    <bean id="person" class="person.Person"/>
    <!--使用@Autowired注解就不用写property标签属性了，也可以在bean后面加autowired属性-->
    
    <bean id="cat" class="person.pet.Cat"/>
    <bean id="dog" class="person.pet.Dog"/>
</beans>
```

person:

```java
public class Person {
   //    flase表示可以为空
    @Autowired(required = false)
    private Cat cat;
    @Autowired
    private Dog dog;
    private String name;

    public String getName() {
        return name;
    }
    public Cat getCat() {
        return cat;
    }
    public Dog getDog() {
        return dog;
    }
    public void shout(){
        System.out.println(cat.getName());
        System.out.println(dog.getName());
    }
}

```



---

# 3. 注解开发

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans
    xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context-3.0.xsd
    ">
    
<!--指定要扫描的包，包下的注解会生效，其实这条写了，支持注解模式那条就已经包含进去了-->
    <context:component-scan base-package="entity"></context:component-scan>
    
    </beans>
```



## 1.`<bean>`标签的autowire属性

不用在person下面写属性了

```xml
<bean id="person" class="person.Person" autowire="byType">
    <property name="name" value="xx"/>
    </bean>
    <bean id="cat" class="person.pet.Cat"/>
    <bean id="dog" class="person.pet.Dog"/>
```

bytype需要保证class唯一

byname需要保证id唯一

## 2.@Component

衍生注解：

  -  @Controller：一般用在表现层
  -  @Service：业务层
   - @Repository：持久层

Person:

```java
//等价于<bean id="person" class="entity.Person">
//    @Component 组件，说明这个包被spring管理了
@Component
public class Person {
    @Autowired
//    @Qualifier(value="dog")
    private Dog dog;
    @Resource
    private Cat cat;
    private String name = "YZY";

    public void shout(){
        dog.shou();
        cat.shout();
    }
    @Override
    public String toString() {
        return "Person{" +
                "dog=" + dog +
                ", cat=" + cat +
                ", name='" + name + '\'' +
                '}';
    }
}
```



---

## 3.使用注解注入数据

 **使用注解注入时就可以不用set方法了**

- ***@Autowired***:
  自动按照类型注入只要容器中有唯一的bean对象类型和注入的类型匹配，就可以注入成功

注意点 用于**匹配指定类型**，但是我拥有两个相同的类所以就会无法匹配到确定的一个类上，这时候再匹配与变量名相同的bean，如果都不对就报错，这时候加上@Qualifier指定变量名

- ***@Qualifier***：对Autowired的补充
      在按照类注入的基础上按变量名注入，在给类成员注入时不能单独使用，但是方法注入时可以
           属性：value 
                  **指定bean的id值**



- ***@Resource*** ：上面两个的集合体

  属性 ：name  
      指定bean的id值

```java
@Autowired
    @Qualifier(value="dog")
    private Dog dog;
    @Resource
    private Cat cat;
```

- @**Value**：用于注入基本类型和string

这两个相等

```java
 @Value("YZY")
    private String name;
//  private String name = "YZY";
```

相当于配置文件中的

```xml
<property name="name" value="YZY"/>
```



##   注解作用范围Scope

相当于配置文件中的<bean>标签中的scope属性

- @Scope ：指定bean的作用范围
  - 属性：value 指定范围的取值 这个和配置文件中一样

*                       @Scope(value = "prototype")

##  注解生命周期（了解）

 - 相当于配置文件中的<bean>标签中的init-method和destroy-method属性
- @PostConstruct:创建方法
   *            @PostConstruct： 创建方法
                public void init(){
                System.out.println("对象出生");}  
   

   
- @PreDestroy :销毁方法
    *           public void destroy(){
                System.out.println("对象死亡");
                }

## xml和注解的选择

   - xml： 更万能，维护简单
   - 注解：不是自己的类用不了，维护复杂
###最佳实践
- xml用来管理bean
- 注解负责属性注入

---
# 4.javaConfig

创建一个**javaconfig**类用于代替spring配置文件，在springboot中都用这种方法

使用**包扫描@ComponentScan**，但是**类**要加上注解**@Component**，才能被扫描到

```java
@Configuration
//加上扫描，不然找不到组件会报错，
// 如果加上了扫描，那么被扫描的对象就不用@Bean了
@ComponentScan("entity")
public class Config {

    
//    加了包扫描，这个就不用写了，自动配置
    /*@Bean
    public Person getPerson(){
        return new Person() ;
    }*/
}
```



也可以创建**多个**config类，然后用@**import** 引用

```java
@Configuration
@Import(Config.class)
public class Config1 {

}
```

测试代码：

```java
@Test
    public void test(){
//        使用javaconfig需要用AnnotationConfig上下文获取容器
       ApplicationContext a = new AnnotationConfigApplicationContext(config.Config.class);
        Person person = a.getBean("person", Person.class);
        person.shout();
    }
```





# 5. 代理模式

为什么要学习代理模式，因为这是SpringAOP的底层<br>
是二十三种设计模式之一，***springAOC和springMVC必考***

- 代理模式的分类   
    - 静态代理
    - 动态代理
    
    ---
    
## 静态代理

角色分析

   - 抽象角色：一般使用接口或者抽象类来解决
   - 真实角色：被代理的角色
   - 代理角色：代理真实角色，并做一些附属操作
   - 客户：访问代理角色的人

步骤：
1. 接口（租房操作）
```java
//租房接口
public interface Rent {
    
    public void rent();
    
}
```

2. 真实角色（房东）

```java
//房东要租出去的房子
public class House implements Rent {
    String housName;

    public String getHousName() {
        return housName;
    }

    public House(String housName){
       this.housName = housName;
    }

    public void rent(){
        System.out.println("房东要出租房子"+housName);
    }
}
```

3. 代理角色（中介）
```java
//代理
public class Proxy implements Rent{

    private House house;

    public Proxy(){

    }
    //代理去帮你租这个房子
    public Proxy(House house){
        this.house = house;
    }
//    房子被代理租出去了
    @Override
    public void rent(){
        house.rent();
        seeHouse(house.getHousName());
        money(house.housName);
        fire(house.getHousName());
    }
    public void seeHouse(String housName){
        System.out.println("中介带你看房"+ housName);
    }
    public void money(String housName){
        System.out.println("中介收费"+ housName);
    }
    public void fire(String housName){
        System.out.println("签合同"+ housName);
    }


}
```
4. 客户端访问代理角色（租客） 
```java
public class Client {
    public static void main(String[] args) {
//        看中了某个房子
        House house = new House("无名公寓");
//        找中介
        Proxy proxy = new Proxy(house);
//        中介的附属操作，不用和房东接触
        proxy.rent();

    }

}
```

### 静态代理好处：

- 使真实角色（租客，房东）更加操作纯粹，不用关注公共业务（看房，签合同）
- 公共业务交给了代理（中介），实现了业务的分工（签合同，看房）
- 公共业务发生拓展的时候，方便管理
###缺点
- 一个真实角色就要一个代理，代码量翻倍，开发效率低

##  静态代理加深理解

有一个service类用于使用增删改查的操作

```java
public class UserServiceImpl implements UserService{

    //        如果我还想再方法里加语句就很麻烦，不方便去改源代码，那么这个时候就要用代理类

    @Override
    public void add() {
        System.out.println("增");
    }

    @Override
    public void delete() {
        System.out.println("删");
    }

    @Override
    public void update() {
        System.out.println("改");
    }

    @Override
    public void query() {
        System.out.println("查");
    }
}
```
现在我想加一个日志功能,能不能在service类里直接修改呢？

最好不要，因为直接修改源代码可能会出错，而且如果代码量多的话十分麻烦，那么我们可以通过一个代理类来增加功能。
```java
public class UserServiceProxy implements UserService {
    private UserService userService;
//    在代理类里调用并加上日志
public void setUserService(UserService userService) {
    this.userService = userService;
}
    public void log(String callname){
        System.out.println("调用了一次"+callname+"方法");
    }
    @Override
    public void add() {
        userService.add();
        log("add");
    }
    @Override
    public void delete() {
        userService.delete();
        log("delete");
    }
    @Override
    public void update() {
        userService.update();
        log("update");
    }
    @Override
    public void query() {
        userService.query();
        log("query");
    }
}
```
这就是只拓展不修改，不在改变原有代码的基础上增加新功能

没有加一层封装解决不了的方法，如果有，就再加一层。

![横切.png](https://upload-images.jianshu.io/upload_images/8007735-07e1ac4c6a3cb130.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
不改变业务的情况下，我们要增加功能，日志就要横切进去，这就是横向开发，这就是切面！

##  动态代理

- 动态代理和静态代理角色是一样的
- 动态代理是动态生成的，不是我们直接写好的
- 分为两大类
    - 基于接口理：基于JDK动态代理（我们学习的）
    - 基于类：cglik
    

需要了解两个类：
- Proxy：代理
    - 提供静态方法方法能创建***动态代理***和实例
- invocationHandler：调用处理程序
    - 每一个代理类都会有一个调用处理程序，当代理类调用该方法，代理类分配到invoke方法通过反射反射
    
### 1 创建一个动态代理

```java
 import java.lang.reflect.Method;//用这个类自动生成代理类
 public class ProxyInvocationhandler implements InvocationHandler {
     //    被代理的接口
     private Object target;
 
     public void setRent(Object target) {
         this.target = target;
     }
     /*newProxyInstance方法的参数：
         classLoader:类加载器
             用于加载代理对象字节码。和被代理对象使用 相同的字节码，固定写法
     class[]：字节码数组
             用于让代理对象和被代理对象有相同的方法。固定写法
     InovocationHandler:用于提供增强的代码
             让我们写如何代理。通常情况下都是匿名内部类，但不是必须 */
     //得到代理类
     public Object getProxy() {
         return Proxy.newProxyInstance(this.getClass().getClassLoader(), target.getClass().getInterfaces(), this);
     }
 
 //    处理实例并返回结果
     @Override
     public Object invoke(Object proxy,Method method,Object[] args){
//                      增强功能
                        log("YY");
//                          返回target类的方法和方法的参数
        return method.invoke(target,args);
}
     public void log(String callname){
         System.out.println("调用了一次"+callname+"方法");
     }
 }
```
### 2 接口代理

```java
public class Client {
public static void main(String[] args){
  final House house = new House();
        /*newProxyInstance方法的参数：
                 classLoader:类加载器
                     用于加载代理对象字节码。和被代理对象使用 相同的字节码，固定写法
             class[]：字节码数组
                     用于让代理对象和被代理对象有相同的方法。固定写法
             InovocationHandler:用于提供增强的代码
                     让我们写如何代理。通常情况下都是匿名内部类，但不是必须 */
//                          前两个都是写被代理类的类加载器
    Rent12 rent12Proxy = (Rent12)Proxy.newProxyInstance(house.getClass().getClassLoader(),house.getClass().getInterfaces,
                      new InvocationHandler() {
                    @Override
                   /* proxy：当前代理对象的引用
                      method:当前执行的方法
                      args：执行方法需要的参数
                    **/
//          这里就相当于代理类得到了真实角色的方法，可以再里面加功能，最后在main方法里调用你想要的代理类.方法
                public Object invoke(Object proxy,Method method,Object[] args) throws Throwable{
                            if("rent".equals(method.getName())){
                                System.out.println("房子被代理了");
                            }
                    return method(house,args);
                }
});
            rent12Proxy.rent();
}
}
```
注意点：代理类的类型为接口类型而不是被代理类的类型

通俗的讲：代理类就是集成被代理类的接口，然后再把被代理类的具体方法方法拿过来用进行可以拓展，

然后集合成一个调用处理程序，想用就直接调用这个程序就行了。

### 3 子类代理

上一个方法是接口代理，那么这个方法就是子类代理，需要用到一个第三方jar包cglib

```java
package 动态代理子类;

import net.sf.cglib.proxy.Enhancer;
import net.sf.cglib.proxy.MethodInterceptor;
import net.sf.cglib.proxy.MethodProxy;
import 动态代理黑马.Rent12;
import 增加日志功能.UserService;
import 增加日志功能.UserServiceImpl;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

/**
 * @author YZY
 * @date 2020/3/31 - 20:33
 */
public class Client {
    public static void main(final String[] args) {
        final House1 house1 = new House1();
        house1.setHouseName("无名公寓");
/*动态代理子类需要第三方的jar包cglib
*   使用Enhancer的create方法
        create方法的参数：
        class:字节码
            用于指定被代理类的字节码，固定写法

    callback:用于提供增强的代码
            让我们写如何代理。通常情况下都是匿名内部类，但不是必须
            一般写的是该接口的子接口实现类：MethodInterceptor*/
//       相比于代理接口，代理子类少了一个参数，getInterfaces（），因为直接代理子类，就不需要子类的接口了
//        这里就直接选择子类类型就行了
      House1 house1Proxy = (House1) Enhancer.create(house1.getClass(), new MethodInterceptor() {
            /*前三个参数和代理接口的参数一样
            * methodProxy：当前执行方法的代理对象
            * 其他内容就和代理接口一模一样*/
            @Override
            public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
                if ("rent".equals(method.getName())) {
                    System.out.println("动态代理接口");
                }
                return method.invoke(house1, args);
            }
      });
        house1Proxy.rent();
    }
}

```

##  动态代理相比于静态代理的优势

静态代理代理的是一个业务，而动态代理继承的是接口，可以代理一类业务，范围范围更广，应用更加灵活

---
# 6.AOP

使用AOP需要导包

```xml
<dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.9.4</version>
        </dependency>
```
## 1 使用spring的API接口

​    配置复杂但是功能强大
先创建继承接口的类

```java
public class methodBeforeLog implements MethodBeforeAdvice {
    @Override
    /*method：方法
    args：参数
    target：目标对象*/
    public void before(Method method, Object[] args, Object target) throws Throwable {
        System.out.println(target.getClass().getName()+"的"+method.getName()+"被执行了");
    }
}
```
```java
package log;
import org.springframework.aop.AfterReturningAdvice;
import java.lang.reflect.Method;
public class afterReturnLog implements AfterReturningAdvice {
    @Override
//    returnValue:返回值
    public void afterReturning(Object returnValue, Method method, Object[] args, Object target) throws Throwable {
        System.out.println("执行了"+target.getClass().getName()+"返回的结果是"+returnValue);
    }
}
```
配置文件：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd">
<!--配置bean-->
<bean id="userService" class="service.userService1Impl"></bean>
    <bean id="beforeLog" class="log.methodBeforeLog"></bean>
    <bean id="afterLog" class="log.afterReturnLog"></bean>

    <!--1.使用原生spring API注入-->
<!--配置AOP：需要导入AOP 的约束-->
    <aop:config>
    <!--需要一个切入点：即去哪个地方执行(去userServiceImpl下的所有方法*，的所有位置（..）)-->
        <aop:pointcut id="pointcut" expression="execution(* service.userService1Impl.*(..))"/>

        <!--执行环绕-->
<!--        把beforelog这个类连接到切入点-->
        <aop:advisor advice-ref="beforeLog" pointcut-ref="pointcut"/>
        <aop:advisor advice-ref="afterLog" pointcut-ref="pointcut"/>
</aop:config>

</beans>
```

## 2 自定义java类实现AOP

​    配置简单但是功能少
先创建一个自定义类

```java
package diy;
public class diyPointcut {

    public void MethodBeforeAdvice(){
        System.out.println("方法执行前");
    }
    public void AfterReturningAdvice(){
        System.out.println("方法返回后");
    }
}
```
然后写入配置文件
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd">
<!--    2.自定义类-->
    <!--配置bean-->
    <bean id="userService" class="service.userService1Impl"></bean>
    <bean id="log" class="log.diyPointcut"></bean>
<!--        aop编写范围-->
        <aop:config>
            <!--    切点可以在切面里面也可以在切面外面，在外面的话一定要在切面的前面（aop约束规定）-->
            <aop:pointcut id="pointcut" expression="execution(* service.userService1Impl.*(..))"/>
<!--            自定义切面，切面是个类，ref引用你要的类-->
            <aop:aspect ref="log">
<!--            通知-->
                <!--这里就可以自定义在什么时候切入，
                    aop：brfore代表执行前-->
                <aop:before method="MethodBeforeAdvice" pointcut-ref="pointcut"/>
                <aop:after-returning method="AfterReturningAdvice" pointcut-ref="pointcut"/>
            </aop:aspect>
        </aop:config>
</beans>

```

## 3 注解

xml文件中添加bean和AOP注解的语句，否则不会生效

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd">
    <bean id="annotation" class="log.AnnotationPointcut"/>
    <bean id="userService" class="service.userService1Impl"/>
<!--    开启AOP注解-->
    <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
</beans>
```
在编写通知注解
```java
package log;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.Signature;
import org.aspectj.lang.annotation.*;
/**
 * @author YZY
 * @date 2020/4/2 - 17:37
 */
// AOP注解
@Aspect    //标志这个类成切面
public class AnnotationPointcut {

//    前置通知
    @Before("execution(* service.userService1Impl.*(..))")
    public void MethodBeforeAdvice(){
        System.out.println("Before");
    }

       /*环绕通知，功能最强大，可以自定义操作
        它可以算是spring框架给我们提供一种手动控制通知（增强）方法的方式，你可以手动在自定义通知（增强）方式
    ProceedingJoinPoint 连接点，可以从切入点里获取信息*/
    @Around("execution(* service.userService1Impl.*(..))")
    public void Around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
        try{
        System.out.println("around before");
//        获取签名
        Signature signature = proceedingJoinPoint.getSignature();
        System.out.println("signature:"+signature);
//        执行
        Object proceed = proceedingJoinPoint.proceed();
        System.out.println("around after");
        }catch (Exception e){
            System.out.println("around throwing");
        }finally {
            System.out.println("around finnal");
        }
    }

//  返回通知，如果抛出异常不会通知
    @AfterReturning("execution(* service.userService1Impl.*(..))")
    public void AfterReturningAdvice(){
        System.out.println("AfterReturning");
    }
    //  异常通知
    @AfterThrowing("execution(* service.userService1Impl.*(..))")
    public void Throwing(){
        System.out.println("AfterThrowing");
    }
//  最终通知，结果不关心是否异常
    @After("execution(* service.userService1Impl.*(..))")
    public void After(){
        System.out.println("After");
    }
}
```
最后观察输出结果发现通知顺序是有问题的，而上面提到环绕通知可以手动控制通知方式，所以建议使用环绕通知来处理通知事件

![图片.png](https://upload-images.jianshu.io/upload_images/8007735-f302f273ec4e4030.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



# 7.整合Mybatis

**SqlSessionTemplate**

-  是 MyBatis-Spring 的==核心==， 可以无缝代替 **SqlSession**
-  是==线程安全==的，可以被多个 DAO 或映射器所共享使用。 

-  管理 session 的生命周期，包含必要的关闭、提交或回滚操作和异常

- 使用 SqlSessionFactory 作为==构造方法==的参数来创建 SqlSessionTemplate 对象。 
- 使用SqlSessionDaoSupport抽象类创建（相比于构造方法省略了 注入sqlSessionTemplate方法）



## 整合步骤

- 导包
  - junit，mybatis，数据库，springaop，**mybatis-spring**

- 整合步骤

1. 配置数据源
2. 创建sqlSessionFactory对象
3. 创建sqlSessionTemplate对象（就是sqlSession）
4. 接口实现类
5. 注入实现类



实体类**Account**：

```java
package pojo;

/**
 * @author YZY
 * @date 2020/4/12 - 17:42
 */
public class Account {

    private int id;
    private String name;
    private float money;
//  剩下的省略
}
```

**mybatis.config.xml**:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <typeAliases>
        <typeAlias type="pojo.Account" alias="Account"/>
    </typeAliases>
</configuration>
```

**mybatis.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:util="http://www.springframework.org/schema/util"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util.xsd">

    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/account?useSSL=true&amp;auseUnicode=true&amp;acharacterEncoding=UTF-8&amp;aautoReconnect=true&amp;afailOverReadOnly=false"/>
        <property name="username" value="root"/>
        <property name="password" value="yzy665128"/>
    </bean>

    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"></property>
        <property name="configLocation" value="classpath:mybatis-config.xml"/>
        <property name="mapperLocations" value="classpath:Dao/IAccountDao.xml"/>
    </bean>

    <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
<!--        构造器注入-->
        <constructor-arg index="0" ref="sqlSessionFactory"/>
    </bean>
</beans>

```

实现类**AccountImpl**

```java
package Dao.Impl;

import Dao.IAccountDao;
import org.mybatis.spring.SqlSessionTemplate;
import pojo.Account;

/**
 * @author YZY
 * @date 2020/4/12 - 17:43
 */
public class AccountImpl implements IAccountDao {


    private SqlSessionTemplate sqlSessionTemplate;

    public void setSqlSessionTemplate(SqlSessionTemplate sqlSessionTemplate) {
        this.sqlSessionTemplate = sqlSessionTemplate;
    }

    @Override
    public Account findByName(int id) {
        IAccountDao iAccountDao = sqlSessionTemplate.getMapper(IAccountDao.class);
        return iAccountDao.findByName(id);
    }
}
```

**applicationcontext.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:util="http://www.springframework.org/schema/util"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util.xsd">

    <import resource="mybatis.xml"/>

<bean id="accountimpl" class="Dao.Impl.AccountImpl">
    <property name="sqlSessionTemplate" ref="sqlSession"/>
</bean>
</beans>

```

测试代码：

```java
@Test
    public void findbyname(){
        ClassPathXmlApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationcontext.xml");
        IAccountDao accountimpl = applicationContext.getBean("accountimpl", IAccountDao.class);
        System.out.println(accountimpl.findByName(1));
    }
```



## 整合与单纯mybatis 的区别

-  在 ==MyBatis== 中，是通过**SqlSessionFactoryBuilder** 来创建 SqlSessionFactory 的。

  ```java
  sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
  ```

-  而在 ==MyBatis-Spring== 中，则使用 **SqlSessionFactoryBean** 来创建 （注入创建）

  ```xml
  <bean id="sqlSessionFactory" class="org.">
   <property name="dataSource" ref="datesource"/>
  </bean>
  ```

  

- 1.2.3.4步是mybatis的整个流程，现在1，2，3整合到了一个xml文件中，后面的交给了spring





# 8.声明式事务

- 一组业务相当于一个业务，要么都成功，要么都失败
- 确保完整性和一致性



 **事务ACID**：

- 原子性
- 一致性
- 隔离性：多个业务操作同一个对象，防止资源损坏
- 持久性



 **使用MyBatis-Spring 的其中一个主要原因是它允许 MyBatis 参与到 Spring 的事务管理中** 

 MyBatis-Spring 借助了 Spring 中的 **DataSourceTransactionManager** 来实现事务管理 



配置事务

```xml

<!--    开启 Spring 的事务处理功能-->
    <bean id="transaction" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>

<!--    集合aop实现事务织入-->
<!--    配置事务通知-->
    <tx:advice id="txAdvice" transaction-manager="transaction" >
<!--        给哪些方法配置事务, propagation：传播，默认REQUIRED-->
        <tx:attributes>
            <tx:method name="add" propagation="REQUIRED"/>
            <tx:method name="delete" propagation="REQUIRED"/>
            <tx:method name="update" propagation="REQUIRED"/>
            <tx:method name="query" read-only="true"/>
            <tx:method name="*" propagation="REQUIRED"/>
        </tx:attributes>
    </tx:advice>

<!--    配置aop-->
    <aop:config>
        <aop:pointcut id="txPointcut" expression="execution(* *..*.*(..))"/>
        <aop:advisor advice-ref="txAdvice" pointcut-ref="txPointcut"/>
    </aop:config>
```

测试语句（add可用，delete不可用）

```java
public void tx(int id, Account account){
        addAccount(account);
        deleteAccount(id);
    }
```

**如果没有配置事务，那么会增加语句而不会删除**

**使用了事务后不会增加。**