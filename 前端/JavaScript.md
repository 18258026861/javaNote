# JavaScript

## 什么是JavaScript

 JavaScript 的正式名称是 "[ECMAScript](https://baike.sogou.com/lemma/ShowInnerLink.htm?lemmaId=47277627&ss_c=ssc.citiao.link)" 

 JavaScript最初的确是受Java启发而开始设计的 ， 但是实际上，JavaScript的主要设计原则源自Self和Scheme，**它与Java本质上是不同的** 

# 快速入门

## 引入js

内部标签

```html
<!--内部标签-->
    <script>

    </script>
```



外部引入

```html
<!--外部引入-->
    <script src="引入.js"></script>
```



## 基本语法

定义所有变量都用var，比Java简单多了

```js
		var a = 1;
        var b = "aaa";
        var c = false;
        var d = {"name":"yzy"}
```

显示数据的方式

1.弹窗

第一个弹出，此时控制台还没有打印数据

```js
 /*弹窗*/
        alert(a);
```

![1587702039448](JavaScript.assets/1587702039448.png)

2.控制台打印

```js
 /*在页面consolo打印变量*/
        console.log(a);
        console.log(b);
        console.log(c);
        console.log(d);
```

![1587702095227](JavaScript.assets/1587702095227.png)

## 浏览器调试

**element**：参看html代码和css样式

**consolo**：打印信息和编写代码

**Source**：打断点debugger

**network**：查看网络请求

**application**：浏览器存储的信息

![1587702293800](JavaScript.assets/1587702293800.png)



# 数据类型

## typeof查看数据类型

```js
console.log(typeof 100);
        var name = "尹振宇";
        console.log(typeof name);
```



## 常量

```js
 //常量需要使用const定义，不可改变,必须有初始值,命名最好是用大写和下划线
        const PI = 3.14;
        console.log(PI);

        // 常量和变量的区别：常量必须有初值，切不可被改变
        //                 变量可以没有初值且可以被改变
        //
        // 常量和字面量的区别：均不会被改变，常量为储存数值的容器，字面量为数值；
```

### 常量和变量的区别

- **常量必须有初值**，切**不可被改变**
- 变量可以没有初值且可以被改变
- 常量和字面量的区别：均不会被改变，常量为储存数值的容器，字面量为数值；

## number

包含

- 整数
- 浮点数
- 科学计数法
- 负数
- **NaN**：非法数值
- **Infinity**：无限大

```js
// number最大值Number.MAX_VALUE
        console.log(Number.MAX_VALUE);  //1.7976931348623157e+308
        //       最小值Number.MIN_VALUE
        console.log(Number.MIN_VALUE);  //5e-324
        //       无穷大 Infinity  Number.MAX_VALUE + Number.MIN_VALUE
        console.log(typeof Infinity);   //number
        //       无穷小 -Infinity
        console.log(typeof -Infinity);  //number

        //NaN非法数字Not A Number
        var num = NaN;
        console.log(typeof NaN);//number

        //浮点数运算可能失去精度
```



## 字符串

字符串可以使用是**单引**号或和**双引号**

```js
var name = 'yzy';
var name1 = "yzy";
```

相同引号不能嵌套，**不同引号可以嵌套**

```js
var name2 = "yzy's'";
```

可以使用``进行多行编写、

```js
var string = `name
        ss
        asfdaf
        asdasd`;
```

**模板字符串**，支持在字符串里EL表达式

```js
var mb = `name,${name}`;
        console.log(mb);  //name,yzy
```

![1587712946594](JavaScript.assets/1587712946594.png)

字符串的==属性==：

- 字符串的**长度**，且它的值不会变

```
string.length
```

- 大小写转换，**这个是方法不是属性**

```
mb.toUpperCase();  //"NAME,YZY"
```

- 索引某个字母的下标indexof

```
mb.indexOf("m");    //2
```

- 截取

```
/*截取，包含前面不包含后面*/
        mb.substring(2,5);  //"me,"
```



## boolean

```js
		var bool1 = true;
        var bool2 = false;
```

- **数值**：任何非零数值都是true，只有0和NaN是false

```js
		var bool3 = Boolean(0);
        var bool4 = Boolean(Number.MAX_VALUE);
        console.log(bool5);     //true
        console.log(bool6);     //false
```

- **字符串**：任何非空字符串都是true，只有空字符串是false

```js
		var bool5 = Boolean('sdasdas');
        var bool6 = Boolean('');
        console.log(bool5);		
        console.log(bool6);
```

- **对象**：任何对象都是true，只有null和undefined是false

```js
        var bool7 = Boolean(null);
        var bool8 = Boolean(undefined);
        console.log(bool7);         //false
        console.log(bool8);         //false
```



## Undefine和null

- **Undefined**：表示变量未赋值，这种类型只有一种值就是undefined

  - **undefined**是Undefined类型的字面量，typeof对没有初始化和没有生命的变量都会返回undefined

  ```js
  		var ll;
          console.log(typeof ll);        //undefined
          var k = undefined;
          console.log(typeof k);          //undefined
  ```

  

- **null**：表示的是一个空的对象，所以**typeof会返回Object**

## 数组[]

一系列**对象**（可以不是同一类型）

Java必须是同一类型

```js
		var array = [1,2,45,"name"];
        console.log(array)     //Array(4) [ 1, 2, 45, "name" ]
		/*计数从0开始*/
        array[1];  //2
        
        
```

**数组的长度可以改变**

```js
 /*数组的长度可以改变，给length赋值*/
        //长度边长，多余的下标为empty，类型为undifined
        array.length = 10;
        console.log(array);   // Array(10) [ 1, 2, 45, "name", null, <5 empty slots> ]

    //    长度变短，会被截取，越界的类型也为undifined
        array.length = 2;
        console.log(array);   //Array [ 1, 2 ]
```

**indexof**,通过元素找到他的下标，有多个该元素时返回第一个

```js
array.indexOf(null);   //4
```

**slice()**，截取一部分返回型数组，类似String的substring

```js
array.slice(2,4);  //Array [ 45, "name" ]
```

**push,pop**：尾部操作

```js
		/*push 和 pop尾部压入和弹出*/
	array.push(1,1,1)   //Array(8) [ 1, 2, 45, "name", null, 1, 1, 1 ]
        array.pop()         //[ 1, 2, 45, "name", null, 1, 1 ]
```

**unshift，shift**：头部操作

```js
        /*unshift和shift 头部压入和弹出*/
array.unshift("a",4); //Array(11) [ "a", 4, 1, 2, 45, "name", null, 1, 1, 1, … ]
array.shift();           //Array(11) [  4, 1, 2, 45, "name", null, 1, 1, 1, … ]
```

**sort**：排序

```js
Array.sort();           //Array(10) [ 1, 1, 1, 1, 2, 2, 4, 45, "name", null ]
```

**reverse** ： 翻转

```js
 array.reverse();        //Array(10) [ null, "name", 45, 4, 2, 2, 1, 1, 1, 1 ]
```

**concat** 添加返回新数组，原数组保持不变

```js
array.concat(1,2); // Array(12) [ null, "name", 45, 4, 2, 2, 1, 1, 1, 1, 1 , 2 ]
console.log(array);;      //Array(10) [ null, "name", 45, 4, 2, 2, 1, 1, 1, 1 ]
```

join ： 打印拼接数组

```js
array.join("--");       //"--name--45--4--2--2--1--1--1--1"
```



如果**越界**，结果为undefined

```js
array[5];   //undefined
```

## 对象{}

相当于java的类，中间用**逗号隔开**，若干个键值对

```js
/*相当于java中的类，Person person = new Person("yzy",21,"man")*/
	var person = {
            name:"yzy",
            age:21,
            sex:"man"
        }
        console.log(person.name);
```

对象可以**赋值**

```js
person.age;      //21
        person.age = 22;    
        person.age;     //22
```

不存在的对象属性**不会报错**，显示undifined

**动态增删属性**

```js
 person.hh = "hh";
        person;         //Object { name: "yzy", age: 22, sex: "man", hh: "hh" }
        delete person.hh
        person          //Object { name: "yzy", age: 22, sex: "man" }
```

**判断是否有该属性**

```js
/*判断是否有该属性,属性名是字符串，要加上引号（单双都行）*/
        'age' in person;;       //true
/*这个对象没有，但是他父类__proto__有*/
        'toString' in person;       // true
```

判断对象本身是否有该属性

```js
person.hasOwnProperty('age');       //true
        person.hasOwnProperty('toString');  //false
```



## Map 和 Set

ES6的新特性

```js
/*本来多个书籍应该这么写*/
        var name = ["yzy","yy","hc"];
        var score = [10,12,5];
        /*用Map*/
        var map = new Map([["yzy",10],["yy",12],["hc",5]])
        var sc = map.get('yzy');
        console.log(sc);   //10

/*      动态新增数据*/
        map.set('ss',100);      //  Map(4) { yzy → 10, yy → 12, hc → 5, ss → 100 }
		/*删除数据*/
        map.delete("ss");       // Map(3) { yzy → 10, yy → 12, hc → 5 }
```

**set**:**无序不重复**的集合

```js
/*set*/
        var set = new Set([3,1,2,5,3,2,1])
        /*发现多余的数值都不显示*/
        console.log(set);       //Set(4) [ 3, 1, 2, 5 ]

		set.add(7);              //Set(5) [ 3, 1, 2, 5, 7 ]
        /*判断是否有该元素*/
        set.has(1);         //true
```

**遍历集合** for()of map)

```js
/*      遍历集合中的键值对*/
        for(var x of map){
            console.log(x)      //Array [ "yzy", 10 ]       Array [ "yy", 12 ]      Array [ "hc", 5 ]
        }
/*遍历Set集合中的元素*/
        for(var y of set){
            console.log(y);             //3,1,2,5,7
        }
```



# 运算符

## 逻辑运算

- && 两个都为真，结果为真，否则为假
- ||一个及以上为真，结果为证
- ！结果相反



## 比较运算符

- =  赋值

```js
var a = 10
```

- ==  等于（类型不一样，值一样，也是true）

```js
/*数值*/
        var a = 10
        /*字符串*/
        var b = "10"
        console.log(a==b)   //  true
```

- === 绝对等于（类型一样值一样）

```js
	/*数值*/
        var a = 10
        /*字符串*/
        var b = "10"
        console.log(a === b);   //false
```

### 注意点

JS的一个缺陷，所以要**使用===**

- NaN 不等于自己

```js
NaN === NaN    // false
/*要判断是否为NaN，使用方法isNaN()*/
        isNaN(NaN)      //true
```

- **会有精度损失**

```js
(1/3 === 1-2/3)     //false
```

尽量避免浮点数运算

## 严格模式

前提，支持es6语法

会扫描全局变量，如果没有改变量就会报错

```
'use strict'	
```



# 流程控制

和java没区别

## if

```js
var tt = 10;
    if(tt>1){
        console.log(1);             //1
    }
```

## while循环

```js
 while(tt<15){
        tt++;
        console.log(tt);      //11,12,13,14,15
    }
```

## for循环

```js
for(var i = 0;i < 5; i++){
       console.log(i);      //0,1,2,3,4
   }
```

## foreach数组循环

```js
var array = [1,2,3,4,2,3];
    //第一种方法，写函数
    array.forEach(function (value) {
        console.log(value);
    })
//    第二种方法,遍历下标
    for (var number in array){
        console.log(array[number]);
    }
```



# 函数

## 定义函数

```js
 /*定义函数方式1*/
    function f1(x) {
        if(x>=5){
            console.log("x大于等于5");
        }else
            console.log("x小于5");
    }
    f1(6);       //x大于等于5

    /*定义函数方式2*/
    var f2 = function(x){
        if(x>=5){
            console.log("x大于等于5");
        }else
            console.log("x小于5");
    }
    f2(4);          //x小于5
```

参数可以随意输入

```js
f1(7,2);       //x大于5，输入多个参数会判断第一个
    f1();       // 不输入参数会判断else
```

在函数里可以判断并且抛出异常

```js
 var f2 = function(x){
        /*可以手动抛出异常*/
        if(typeof x!=="number"){
            throw "not a number";
        }
        }
```

![1587743522109](JavaScript.assets/1587743522109.png)

arguements：参数数组

```js
var f3 = function (x) {
        /* arguments :传入参数形成的数组
        *   比如我传入很多参数，但是我接收的第一个参数，而arguements接收的就是全部参数形成的数组*/
        console.log(x);
        for(i = 0;i < arguments.length; i++){
            console.log(arguments[i]);
        }
    }

    /*输入多个参数*/
    f3(2,5,4,7,8);       //实际接收的数是x=2，arguements的数组是2，5，4，7，8
```

![1587806032087](JavaScript.assets/1587806032087.png)

有这个arguements，又出现了一个新问题，如果我要**获取除了接收的参数以外的其他参数**用于进行其他操作该怎么办

使用ES6 引入的新参数**rest**

```
/*rest,相当于java的可变参数*/
    var f4 = function (x,...rest) {
        console.log(x);
        console.log(rest);
    }
    f4(1,5,7,3);            //x = 1    rest = Array(3) [ 5, 7, 3 ]
```



## 变量的作用域

在javascript中，**var变量是有作用域的**

假设在**函数内定义的变量**，在外面是不可以用的，会报错

```js
function f() {
        var x = 1;
    }
    console.log(x);         //报错：ReferenceError: x is not defined
```

两个函数内的变量互不相干，可以同名

```js
*   f1函数也可以使用x变量*/
    function f1() {
        var x = 2;
    }
```

嵌套函数，内部可以访问外部变量，外部不可以访问

```js
function f2() {
        var x = 1;
        function f3() {
            /*内部可以访问外部变量，外部不可以访问*/
            var y = x;
            console.log(y);
        }
    }
```

**自动提升变量声明**，但是不会提升变量赋值

```js
function f3() {
        var x = y;
        console.log(x);         //显示的是undefined，意思是有y这个变量，但是没有赋值，即var y;
        /*y定义在x的后面，  为什么不报错呢，
        原因是var y = 10 可以分成两步 
        1。 var y;                 
        2. y = 10;同理，
      var x = y ，也是两步  ，JS自动提升变量声明， 把var y提升到了x = y 的前面，所以有了undefined */
        var y = 10;
    }
```

所以尽量把所有变量定义在开头，便于代码维护

```js
/*代码改进,定义放最开头*/
    function f4() {
        var x,y;
        y = 10;
        x = y;
    }
```



### 全局变量

Javascript就是一个全局作用域。我们所有的变量都绑在全局上。但是如果我们引入多个JS文件，中间存在相同的变量怎么解决冲突

可以每个JS文件 创建一个全局空间，把这JS的文件全部加入这个全局空间内

例如jQuery。jQuery.

​						用$代替



### 局部作用域let

```js
/*局部作用域冲突*/
    function f5() {
        for(var i = 0;i<2;i++){

        }
        console.log(i);     //2 ,发现i可以再其他地方被输出，但是我不想，就可以用let
    }
```

使用let解决作用作用域冲突，let只在本作用域下有效

```js
 /*使用let解决局部作用域冲突,let只在本作用域下有效*/
    function f6() {
        for(let i=0;i<2;i++){

        }
        console.log(i);     //ReferenceError: i is not defined
    }
```

建议用let去定义局部作用域的变量



## 方法

定义方法，把函数放在对象中

```JS
/*  对象有两个东西，属性和方法*/
        var person = {
            name : "yzy",
            birth : 1998,
            age : function () {
                /*获取当前年份*/
                var now = new Date().getFullYear();
                return now - this.birth;
            }
        };
        //方法需要带（）
        console.log(person.age());      // 22
```

 ==**this**==

怎么理解this呢？

我们可以把方法拉出来

**这里解释一下调用函数带不带括号问题**：

- 函数名是指针，如果不带括号代表执行这个指针，那么就是寻找他，会显示函数的信息

  加括号代表直接执行花括号里的代码

- 而在**方法的属性调用函数**时，这不**能加括号**，因为因为加括号是直接执行方法，而你需要读取this.birth这个属性，所以要指向函数指针

- 调用**对象的方法**是就需要**带括号**了

```js
var person2 = {
        name : "yzy",
        birth : 1998,
    /*这里不能加括号，因为加括号是直接执行方法，而你需要读取this.birth这个属性，所以要指向函数指针*/
        age : getAge()
    };

    //调用对象的方法需要带括号，否则指向的是函数名指针
    console.log(person2.age());      //22
    console.log(getAge());           //NaN
	

        /*这两个差别是什么，person.age()这个方法是person在调用，所以this读取的就是person里的birth
        *                   而getAge()是全局在调用，而全局没有这个属性，this读取不到，所以为NaN
        *       由此可得，this 被谁调用，就读取谁的属性*/
```

由此可得，**this 被谁调用，就读取谁的属性**

如果我就想在**全局里**拿到该属性可不可以？可以！

==**apply**==

使用apply指定想要获取哪个对象的属性

```js
/*使用apply指向你想要获取属性的对象，第二个是参数，可以填空*/
        console.log(getAge.apply(person2, []));                 //22  
        // 在使用apply时函数也不能加括号，否则会立刻执行
```



# 内部对象

标准对象

```js
typeof 1
"number"
typeof ""
"string"
typeof true
"boolean"
typeof null
"object"
typeof []
"object"
typeof {}
"object"
typeof undefined
"undefined"
typeof getAge
"function"
```



## Date

```js
var time = new Date();
        console.log(time);          //Date Sat Apr 25 2020 22:50:49 GMT+0800 (中国标准时间)
```

![1587826434708](JavaScript.assets/1587826434708.png)

![1587826557827](JavaScript.assets/1587826557827.png)



## JSON

- **一种轻量级的数据交换格式**

- **简洁和清晰的层次结构**
- 易于阅读和编写，也易于机器解析，有效提升网络传输效率



在javascript中，一切都是对象，任何js类型都支持JSON

格式

- 对象都用{}
- 数组都用[]
- 所有的键值对都用key:value

==JSON与JavaScript的互相转化==

```js
        var person = {
            name:"yzy",
            age:21,
            sex:"man"
        };
        console.log(person);   //Object { name: "yzy", age: 21, sex: "man" }

        /* 将对象转化为JSON*/
        let JSONPerson = JSON.stringify(person);
        console.log(JSONPerson);            //{"name":"yzy","age":21,"sex":"man"}

        /*将JSON转化为JavaScript对应类型*/
        let parse = JSON.parse(JSONPerson);
        // 里面和外面不能都是双引号或单引号
        let parse2 = JSON.parse('{"name":"yzy","age":21,"sex":"man"}');
        console.log(parse);             //  Object { name: "yzy", age: 21, sex: "man" }
        console.log(parse2);            //  Object { name: "yzy", age: 21, sex: "man" },两者效果一样
```

JSON和对象的不同点

- {"name":"yzy","age":21,"sex":"man"}  ： JSON 的 key**带引号**
- Object { name: "yzy", age: 21, sex: "man" }   ： 对象的key**不带引号**



# 面向对象编程

**Java中**

类：模板

对象：具体表现

JavaScript中

原型：





```js
/*类*/
        var person = {
            name : "yzy",
            run : function () {
                console.log(this.name + "run");
            }
        };

        /*对象*/
        var YY = {
            name : "YY",
            //如果我想实现person类的run方法，该怎么办呢
            };


        // YY.run();       //SyntaxError: expected expression, got '}'

        /*使对象的原型是person
        *   把YY的原型指向了person*/
        YY.__proto__ = person;

        YY.run();       //YYrun   继承了之后就可以调用父类方法
    //    在YY中的prototype里发现了person的属性和方法，即想在YY的prototype是person
```

在YY中的prototype里发现了person的属性和方法，即想在YY的prototype是person

![1587828962424](JavaScript.assets/1587828962424.png)