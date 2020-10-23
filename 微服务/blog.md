# Blog

搭建个人博客

时间线：

5.14-5.16：type部分

5.17-5.19：blog部分

## 需求分析

### 用户

- 在**主页**查看**热门博客**
- 查看所有博客
- 通过**分类**查看分类下的博客
- 通过**标签**查看拥有该标签的博客
- 通过**关键字搜索**博客
- **评论**
- 通过**顺序**（时间，热门量，评论数）排列博客



### 管理员

- **登录**后台管理
- 管理博客
  - 增删改查
  - 添加标签
  - 设置分类
  - 删除评论
  - 回复评论
- **标签**
  - 查看标签
  - 添加标签
  - 删除标签
  - 修改标签
- **分类**
  - 查看分类
  - 增加分类
  - 修改分类
  - 删除分类



### 功能

![1589424767504](blog.assets/1589424767504.png)



## 设计

使用Axure



### 前端展示

- 导航栏（用户）

  - 首页
  - 分类
  - 标签
  - 搜索
  - 关于作者

  

  

  

- 底部栏

  - 联系我

  

- 首页

  - 显示热门博客和top的标签和分类

  

- 博客内容

  - 标题
  - 内容
  - 时间
  - 评论
  - 推荐（同分类下）

  

- 分类页

  - 显示所有分类
  - 分类里显示分类博客

  

- 标签页

  - 显示所有标签
  - 标签里显示标签博客



### 后端管理

- 导航栏（管理员）

  - 博客管理
  - 分类管理
  - 标签管理

  

- 登录页

- 博客管理页

  - 增删改查

- 标签管理页

  - 管理标签下的博客

- 分类管理页

  - 管理分类下的博客



# 开发

## 后端管理

### 实体类

blog 与其他实体类的关系

- 与Type多对一
- 与Tag  多对一
- 与comment一对多

![1589509166579](blog.assets/1589509166579.png)

- blog包含的内容

![1589509293399](blog.assets/1589509293399.png)



### 登录

> 流程

- 访问登录页面
  - 地址栏输入admin/tologin 
  - 访问admin下的资源路径shiro触发登录
- 输入账号和密码
  - 如果为空，ajax失焦提示
  - 如果不正确，返回tologin页面并且显示错误原因
- 登陆之后默认进入blogs管理界面，导航栏显示用户信息



![1589711256958](blog.assets/1589711256958.png)

> 代码

1.在admin下创建login页面用于登录操作

```java
@RequestMapping("/login")
    public String login(String username,String password,Model model,HttpSession session){
        //        获取当前用户
        Subject subject = SecurityUtils.getSubject();
//          封装前端传来的信息
        UsernamePasswordToken token = new UsernamePasswordToken(username,password);
        try {
            //        为当前用户登录该账号密码，这个方法会在realm中验证是否正确然后异常
            subject.login(token);
            session.setAttribute("admin",username);
            return "admin/blogs";
        }catch (UnknownAccountException e){
            model.addAttribute("msg","此账号不存在");
            return "admin/login";
        }catch (IncorrectCredentialsException e){
            model.addAttribute("msg","密码错误");
            return "admin/login";
        }

    }
```

2.使用**shiro**管理登录安全，拦截所有admin下的请求

```java
@Bean
    public ShiroFilterFactoryBean bean(DefaultWebSecurityManager manager){
        ShiroFilterFactoryBean bean = new ShiroFilterFactoryBean();

        HashMap<String,String> map = new HashMap<>();
//        这边的拦截路径不能设置/admim/*，会拦截掉登录请求
        map.put("/admin/blogs","authc");
        map.put("/admin/input","authc");
        map.put("/admin/info","authc");
        map.put("/admin/types","authc");
        map.put("/admin/tags","authc");
//      设置过滤路径
        bean.setFilterChainDefinitionMap(map);
//        将当前用户纳入安全管理
        bean.setSecurityManager(manager);
//      设置登录页面路径
        bean.setLoginUrl("/admin/tologin");
//        bean.setSuccessUrl("/admin/");
        return bean;
    }
```

3.ajax失焦验证

```html
function a() {
            $.ajax({
                url : "/admin/username",
                //data用于给controller传递值
                data : {"username" :$("#username").val()},
                success :function (data) {
                    $("#usernameInfo").css("color", "red");
                    $("#usernameInfo").html(data)
                }
            })
        };
```

```java
 /*验证模块*/
    @RequestMapping("/username")
    @ResponseBody
    public String username(String username){
        String info = "";
        if(username.length()==0){
            info="不能为空";
        }
        return info;
    }
```





### 分类管理

> 功能

- 查询所有分类
  - 分类页面，显示所有分类
- 添加分类
  - 分类页面可以添加分类
  - ajax失焦判断是否存在该分类
- 删除分类
  - 分类页面分类表的操作栏可以删除
- 修改分类
  - 分类页面的分类表的操作栏可以修改
  - 通过获取id，跳转修改页面：查询显示对应的分类并修改

![1589711033547](blog.assets/1589711033547.png)

> 代码

1.查询

```java
@RequestMapping("/types")
    public String types(Model model){
        List<Type> types = typeService.queryTypes();
        model.addAttribute("types",types);
        return "admin/types";
    }
```

2.添加

```java
@RequestMapping("/add")
    public String add(String type,HttpSession session){
        Type type1 = typeService.queryTypeByName(type);
        if(type1==null){
            typeService.addType(type);
            session.setAttribute("info","添加成功");
        }
        return "redirect:/admin/types";
    }
```

3.删除

```java
@RequestMapping("/delete")
    public String delete(String name,HttpSession session){
        int i = typeService.deleteType(name);
        if(i>0){
            session.setAttribute("info","删除成功");
        }else
            session.setAttribute("info","删除失败");
        return "redirect:/admin/types";
    }
```

4.修改

```java
@RequestMapping("/update")
    public String update(Type type,HttpSession session){
        int i = typeService.updateType(type);
        if (i>0){
            session.setAttribute("info","修改成功");
        }else{
            session.setAttribute("info","修改失败");
        }
        return "redirect:/admin/types";
    }
```



### 博客管理

- 显示所有博客
  - 登录首页显示所有博客
  - 显示博客的部分信息：id，标题，类型，推荐，更新时间
- 查询博客
  - 博客页面上方有搜索栏，提供标题模糊查询和类型查询
- 博客修改
  - 博客右方有修改按钮，点击跳转携带博客id前往修改页面显示具体博客信息
- 博客删除
  - 博客右方有删除按钮，点击通过id删除对应博客

- 增加博客
  - 首页新增按钮跳转编写博客页面
  - 点击创建写入数据库

![1589897244165](blog.assets/1589897244165.png)

![1589897543129](blog.assets/1589897543129.png)

1.博客首页

```java
 /*跳转模块*/
   @RequestMapping("/blogs")
    public String adminblogs(Model model){
        model.addAttribute("blogs",blogService.queryBlogs());
        model.addAttribute("types",typeService.queryTypes());
        return "admin/blogs";
    }
```

2.标题和分类模糊查询

```java
@RequestMapping("/query")
    public String query(@RequestParam(value = "title",required = false)String title,
                        @RequestParam(value = "typeId",required = false)Long typeId,
                        Model model){
        SearchBlog blog = new SearchBlog(title,typeId);
        model.addAttribute("types",typeService.queryTypes());
        model.addAttribute("blogs",blogService.queryBlogByTitleAndType(blog));
        return "admin/blogs";
    }
```

mybatis**模糊查询**

```xml
<select id="queryBlogByTitleAndType" parameterType="SearchBlog" resultMap="blog">
        select b.published,b.id bid,b.title,t.name tname,b.create_time,b.type_id btid,t.id tid,t.name tname from t_blog b,t_type t
        <where>
            b.type_id = t.id
            <if test="title!=null">
                and b.title like CONCAT(CONCAT('%',#{title}),'%')
             </if>
             <if test="typeId !=null">
                and t.id = #{typeId}
             </if>
        </where>
    </select>

<resultMap id="blog" type="Blog">
        <id property="id" column="bid"/>
        <result property="content" column="content"/>
        <result property="createTime" column="create_time"/>
        <result property="firstPicture" column="first_picture"/>
        <result property="pblished" column="published"/>
        <result property="title" column="title"/>
        <result property="updateTime" column="update_time"/>
        <result property="views" column="views"/>
        <result property="typeId" column="type_id"/>
        <association property="type" javaType="Type">
            <id property="id" column="tid"/>
            <result property="name" column="tname"/>
        </association>
    </resultMap>
```

3.增加博客

```java
@RequestMapping("/add")
    public String add(String title,boolean pblished,Long typeId,String content,
    @RequestParam(value = "firstPicture",required = false)String firstPicture,
                      Model model){

        Blog blog1 = blogService.queryBlogByName(title);
        if(blog1==null){
            Blog blog = new Blog();
            blog.setTitle(title);
            blog.setContent(content);
            blog.setTypeId(typeId);
            blog.setCreateTime(new Date());
            blog.setUpdateTime(new Date());
            blog.setFirstPicture(firstPicture);
            blog.setPblished(pblished);
            blog.setViews(0);
            int i = blogService.addBlog(blog);
            if(i>0){
                model.addAttribute("blogInfo","添加成功");
            }else
                model.addAttribute("blogInfo","添加失败");
            model.addAttribute("blogs",blogService.queryBlogs());
            model.addAttribute("types",typeService.queryTypes());
        }else
            model.addAttribute("blogInfo","该标题已存在");
        return "admin/blogs";
    }
```

4.删除博客

```java
@RequestMapping("/delete")
    public String delete(Long id,Model model){
        int i = blogService.deleteBlog(id);
        if(i>0){
            model.addAttribute("bloginfo","删除成功");
        }else
            model.addAttribute("bloginfo","删除失败");
        model.addAttribute("blogs",blogService.queryBlogs());
        model.addAttribute("types",typeService.queryTypes());
        return "admin/blogs";
    }
```

5.跳转页面并修改博客

```java
@RequestMapping("/blogs-edit")
    public String blogsedit(Long id,Model model){
        model.addAttribute("blog",blogService.queryBlogById(id));
        model.addAttribute("types",typeService.queryTypes());
        return "admin/blogs-edit";
    }

@RequestMapping("/update")
    public String update(Long id, String title,boolean pblished, Long typeId, String content,
                      @RequestParam(value = "firstPicture",required = false)String firstPicture,
                      Model model){
        Blog blog = new Blog();
        blog.setId(id);
        blog.setTitle(title);
        blog.setContent(content);
        blog.setTypeId(typeId);
        blog.setUpdateTime(new Date());
        blog.setFirstPicture(firstPicture);
        blog.setPblished(pblished);
        int i = blogService.updateBlog(blog);
        if(i>0){
            model.addAttribute("bloginfo","修改成功");
        }else
            model.addAttribute("bloginfo","修改失败");
        model.addAttribute("blogs",blogService.queryBlogs());
        model.addAttribute("types",typeService.queryTypes());
        return "admin/blogs";
    }
```



### 评论管理



## 前端展示



### 复用

#### 导航栏

- 跳转首页
- 跳转分类页面
- 跳转归档
- 跳转关于我
- 博客搜索

![1589961423537](blog.assets/1589961423537.png)

```html
<!--导航-->
<nav class="ui inverted attached segment m-padded-tb-mini " th:fragment="nav">

<a th:href="@{/}" class=" m-item item m-mobile-hide"><i class="home icon"></i>首页</a>
            <a th:href="@{/types}" class="item "><i class="idea icon"></i>分类</a>
            <a th:href="@{/archives}" class="item"><i class="clone icon"></i>归档</a>
            <a th:href="@{/about}" class="item"><i class="info icon"></i>关于我</a>
    
    <!--    搜索框-->
                    <form th:action="@{/search}">
                    <input name="title" type="text" placeholder="Search....">
                    <i class="search link icon"><input type="submit"> </i>
                    </form>
```





#### 底部

- 二维码

- 联系方式
- 博客信息

![1589961411999](blog.assets/1589961411999.png)

```html
<!--底部footer-->
<footer  class="ui inverted vertical segment m-padded-tb-massive"th:fragment="foot">
    
    <!--联系方式-->
            <div class="three wide column">
                <h4 class="ui inverted header m-text-thin m-text-spaced ">联系我</h4>
                <div class="ui inverted link list">
                    <a href="#" class="item m-text-thin">Email：w1061603811@gmail.com</a>
                    <a href="#" class="item m-text-thin">QQ：1061603811</a>
                </div>
            </div>
            <!--博客介绍-->
            <div class="seven wide column">
                <h4 class="ui inverted header m-text-thin m-text-spaced ">Blog</h4>
                <p class="m-text-thin m-text-spaced m-opacity-mini">这是yzy个人博客</p>
            </div>
```



### 首页

- 默认页面为首页
- 左侧为全部博客信息
  - 博客包含标题，简介，作者，时间，浏览量，首图和所属的分类

- 右侧为分类信息和推荐博客

- 点击博客跳转博客具体信息页面





1.显示博客信息

```html
 <!--导航-->
  <div th:insert="~{/common/common::nav}"></div>
  
  		<!--显示博客数量-->
              <div class="right aligned column">
                共 <h2 class="ui orange header m-inline-block m-text-thin" th:text="${blogs.size()}"></h2> 篇
              </div>

			<!--博客标题，点击跳转-->
                  <h2  class="ui header" ><a th:href="@{/blog(id=${blog.getId()})}" th:text="${blog.getTitle()}" class="m-black" ></a></h2>
                  <!--博客简介-->
                  <p class="m-text" th:text="${blog.getDescription()}"></p>

		<!--作者头像-->
<img src="https://unsplash.it/100/100?image=1005" th:src="@{/images/jlq.png}" alt="" class="ui avatar image">
         <!--作者-->
<div class="content"><a href="#" class="header" th:text="${blog.author}"></a></div>
 		<!--更新时间-->
 <i class="calendar icon"></i><span th:text="${#dates.format(blog.updateTime,'yyyy-MM-dd')}"></span>
 		<!--浏览量-->           
      <i class="eye icon"></i> <span th:text="${blog.getViews()}"></span>
		<!--博客所属分类-->
		<a th:href="@{/type}" target="_blank" class="ui teal basic label m-padded-tiny m-text-thin" th:text="${blog.getType().getName()}"></a>
         <!--首图-->
        <img th:src="${blog.getFirstPicture()}" alt="" class="ui rounded image">
```

2.搜索

```java
@RequestMapping("/search")
    public String search(@RequestParam(value = "title",required = false) String title, Model model){
        SearchBlog blog = new SearchBlog();
        blog.setTitle(title);
        model.addAttribute("types",typeService.queryTypes());
        model.addAttribute("blogs",blogService.queryBlogByTitleAndType(blog));
        return "index";
    }
```



![1589963480014](blog.assets/1589963480014.png)



### 分类页

- 上面显示所有分类和分类拥有的博客数量
  - 点击分类显示分类拥有的所有博客信息

- 第一次跳转显示所有博客信息，点击分类显示该分类下的博客信息

```html
<!--分类名和博客数量-->
          <a th:href="@{/queryblogsbytype(id=${type.getId()})}" class="ui basic teal button" th:text="${type.getName()}"></a>
          <div class="ui basic teal left pointing label" th:text="${#arrays.length(type.blogs)}"></div>
```

1.点击分类

```html
<a th:href="@{/queryblogsbytype(id=${type.getId()})}" class="ui basic teal button" th:text="${type.getName()}"></a>
```

跳转

```java
@RequestMapping("/queryblogsbytype")
    public String queryblogsbytype(Long id,Model model){
        model.addAttribute("blogs",typeService.queryBlogsByType(id));
        model.addAttribute("types",typeService.queryTypes());
        return "types";
    }
```



### 博客详情页

- 通过点击标题跳转到博客具体页面‘
- 显示作者，时间，浏览数，正文，简介，博客信息
- 悬浮条（返回顶部，目录，留言）

- 文章正文需要将content转换成markdown格式才能正常显示



1.转换markdown

引入依赖

```xml
<!-- https://mvnrepository.com/artifact/com.atlassian.commonmark/commonmark -->
<dependency>
    <groupId>com.atlassian.commonmark</groupId>
    <artifactId>commonmark</artifactId>
    <version>0.14.0</version>
</dependency>
```

创建工具类

```java
public class MarkdownUtils {
    public static String markdownToHtml(String markdown) {
        Parser parser = Parser.builder().build();
        Node document = parser.parse(markdown);
        HtmlRenderer renderer = HtmlRenderer.builder().build();
        return renderer.render(document);
    }
    /**
     * 增加扩展[标题锚点，表格生成]
     * Markdown转换成HTML
     * @param markdown
     * @return
     */
    public static String markdownToHtmlExtensions(String markdown) {
        //h标题生成id
        Set<Extension> headingAnchorExtensions = Collections.singleton(HeadingAnchorExtension.create());
        //转换table的HTML
        List<Extension> tableExtension = Arrays.asList(TablesExtension.create());
        Parser parser = Parser.builder()
                .extensions(tableExtension)
                .build();
        Node document = parser.parse(markdown);
        HtmlRenderer renderer = HtmlRenderer.builder()
                .extensions(headingAnchorExtensions)
                .extensions(tableExtension)
                .attributeProviderFactory(new AttributeProviderFactory() {
                    public AttributeProvider create(AttributeProviderContext context) {
                        return new CustomAttributeProvider();
                    }
                })
                .build();
        return renderer.render(document);
    }
    /**
     * 处理标签的属性
     */
    static class CustomAttributeProvider implements AttributeProvider {
        @Override
        public void setAttributes(Node node, String tagName, Map<String, String> attributes) {
            //改变a标签的target属性为_blank
            if (node instanceof Link) {
                attributes.put("target", "_blank");
            }
            if (node instanceof TableBlock) {
                attributes.put("class", "ui celled table");
            }
        }
    }
    public static void main(String[] args) {
        String table = "| hello | hi   | 哈哈哈   |\n" +
                "| ----- | ---- | ----- |\n" +
                "| 斯维尔多  | 士大夫  | f啊    |\n" +
                "| 阿什顿发  | 非固定杆 | 撒阿什顿发 |\n" +
                "\n";
        String a = "[ONESTAR](http://122.51.28.187:8080/)";
        System.out.println(markdownToHtmlExtensions(a));
    }
}
```

业务

```java
 @Override
    public Blog markToHTML(Long id) {
        Blog blog = blogMapper.queryBlogById(id);
        String content = blog.getContent();
//        转换
        blog.setContent(MarkdownUtils.markdownToHtmlExtensions(content));
        return blog;
    }
```



### 前端一些小细节

#### 复用代码+高亮

复用代码设置

```html
  <!--复用-->
<nav class="ui inverted attached segment m-padded-tb-mini " th:fragment="nav">
    <div class="ui container">
        <div class="ui inverted secondary stackable menu">
            <!--  logo-->
            <h2 class="ui teal header item">管理后台</h2>
            <a th:href="@{/admin/blogs}" th:class="${active=='blogs'?'m-item item m-mobile-hide active':'m-item item m-mobile-hide'}"><i class="home icon"></i>管理首页</a>
            <a th:href="@{/admin/types}" th:class="${active=='types'?'item active':'item'}"><i class="idea icon"></i>管理分类</a>
            <a th:href="@{/admin/tags}" th:class="${active=='tags'?'item active':'item'}"><i class="tags icon"></i>管理标签</a>
            <div class="right menu">
                <div class="ui dropdown item">
                <!--      头像图-->
                    <img th:src="@{/images/jlq.png}" class="ui avatar image">
                    <span th:text="${session.admin}"></span>
                    <a th:href="@{/admin/logout}" class="item">注销</a>
                </div>
            </div>
        </div>
    </div>
</nav>
```

引用

```xhtml
<div th:replace="~{/common/admin-common::nav(active='types')}"></div>
```





#### ajax失焦校验

失焦触发

```html
<input type="text" id="typename" name="type" placeholder="新增分类名称" onblur="a()">

<script>
    function a() {
      $.ajax({
        url : "/type/typeExist",
        //data用于给controller传递值
        data : {"type" :$("#typename").val()},
        success :function (data) {
          $("#typeInfo").css("color", "red");
          $("#typeInfo").html(data)
        }
      })
    };
</script>
```

controller

```java
@RequestMapping("/typeExist")
    @ResponseBody
    public String username(String type){
        String info = "";
        Type type1 = typeService.queryTypeByName(type);
        if(type1 != null){
            info="该分类已存在";
        }
        return info;
    }
```

#### 提示框

```html
<div class="ui success message" th:unless="${#strings.isEmpty(session.info)}" id="info">
          <!-- 点击隐藏info提示栏-->
        <i class="close icon" onclick="document.getElementById('info').style.display='none'"></i>
        <div class="header">提示: </div>
        <p th:text="${session.info}"></p>
      </div>
```



#### 下拉框的th：each要放在下拉框的里面

```html
<div class="menu" >
                <div th:each="type:${types}" class="item" th:data-value="${type.id}" th:text="${type.getName()}"></div>
              </div>
```



#### 表单获取后端数据

1.在表单获取对象，在输入框使用*{}

```html
<form th:action="@{/blog/update}" method="post" class="ui form" th:object="${blog}">
```

```html
<input type="hidden" name="id" th:text="*{id}" readonly="readonly">
```

2.直接在输入框使用${}

```html
<input type="hidden" name="id" th:text="${blog.id}" readonly="readonly">
```

#### 输入框不能为空

```js
$('.ui.form').form({
      fields : {
        title : {
          identifier: 'title',
          rules: [{
            type : 'empty',
            prompt: '标题：请输入博客标题'
          }]
        },
        content : {
            identifier: 'content',
            rules: [{
                type : 'empty',
                prompt: '内容：请输入博客内容'
            }]
        },
          typeId : {
              identifier: 'typeId',
              rules: [{
                  type : 'empty',
                  prompt: '分类：请输入博客分类'
              }]
          },
      }
    });
```

#### 时间显示设置

数据库存储的时间精确到秒，前端不需要显示这么精确，只需要显示年月日

```html
<span th:text="${#dates.format(blog.updateTime,'yyyy-MM-dd')}"></span>
```



# 问题

1.资源替换不生效

  删除target，重启



2.使用shiro无法登录

  登录controller中token的参数要填username，password

```
        UsernamePasswordToken token = new UsernamePasswordToken(username,password);
```



3.shiro登录无限跳转登录页面

 shiro拦截掉所有的admin下的请求包括登录，所以不能拦截登录请求



4.controller跳转失败，路径乱码

html页面跳转忘记加th： 模板



5.新增添加分类返回分类页显示500，但是能够添加成功

​	测试了一下，如果不执行添加分类业务就能直接跳转，但是执行业务就会无法解析thymeleaf

​    添加thymeleaf的依赖，解决了模板引擎的问题，但是报错No setter found for the keyProperty 'id' in 'java.lang.String'.

​	设置id的setget方法，将mybatis的insert插入语句

```xml
<insert id="addType" parameterType="java.lang.String" useGeneratedKeys="true" keyProperty="id" >
        insert into t_type(name) values (#{name})
    </insert>
```

  然后出现另一个问题SelectKey returned no data； 将 useGeneratedKeys="true" 删除。这个是返回id



6.ajax 无法返回数据：

 路径写错，将type写成types



7.ajax后端接受的参数为null

   form提交的方式为post，在iajax 中指明type ："post"，或者form中删除post



8.博客查询没有显示

​	1.debug发现controller传参typeid为0，而不是null

​	name=“typeId”放在了下拉框的选项上，应该放到下拉框本身

![1589804174594](blog.assets/1589804174594.png)

   2.改变之后不选择分类或者标题会报错，不能为空，那么接受参数不能使用BlogSearch，而是分开接受，使用@RequestParam并且可以为空

```java
@RequestMapping("/query")
//                      required可以为空
    public String query(@RequestParam(value = "title",required = false)String title,@RequestParam(value = "typeId",required = false)Long typeId, Model model){
        SearchBlog blog = new SearchBlog(title,typeId);
        model.addAttribute("types",typeService.queryTypes());
        model.addAttribute("blogs",blogService.queryBlogByName(blog));
        return "admin/blogs";
    }
```



9.$ is not defined

​	将引用jquery的script标签放到使用$的标签前面