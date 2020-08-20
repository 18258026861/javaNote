# 项目代码
application.yaml中指定环境为dev
![20200811135201](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200811135201.png)

## application项目环境
- server.servlet.context-path:应用上下文，即项目路径，这里指定的是xl-home-api项目
![20200811141115](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200811141115.png)
- redis配置端口及帐号密码
![20200811141038](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200811141038.png)
- 连接mysql数据库
  ![20200811142126](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200811142126.png)
- mybatis-plus文件
  ![20200811142220](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200811142220.png)

# 数据库分析
1.根据实体类，写出数据库各个表

2.sysUser表，sysDepart之间通过一个sysUserDepart表连接



# 登陆系统
要从登录的controller下手
## LoginController
这里只讨论网页登陆

### 帐号密码验证功能
```java
@ApiModel(value="登录对象", description="登录对象")
public class SysLoginModel {
	@ApiModelProperty(value = "账号")
    private String username;
	@ApiModelProperty(value = "密码")
    private String password;
	@ApiModelProperty(value = "验证码")
    private String captcha;
}

@RequestMapping(value = "/login", method = RequestMethod.POST)
	@ApiOperation("登录接口")
				//-RequestBody 将接受的请求数据给变量
	public Result<JSONObject> login(@RequestBody SysLoginModel sysLoginModel) throws Exception {
		Result<JSONObject> result = new Result<>();
					//-获取用户名
		String username = sysLoginModel.getUsername();
		// 前端密码加密，后端进行密码解密，防止传输密码篡改等问题
		String password = AesEncryptUtil.desEncrypt(sysLoginModel.getPassword());
					//-判断密码是否为空
		if(StringUtils.isBlank(password)) {
			result.error500("用户名或密码错误");
			return result;
		}
```
>对上面代码的**分析**

1.将请求的数据（由SysLoginModel这个实体类的字段可得，接受的数据是前段提交的帐号，密码和验证码）赋给sysLoginModel

2.获取数据中的**用户名**

3.前端在传送密码会加密，这里对加密的密码进行**解密**

4.解密之后**判断**密码是否为空。

上述过程都只是简单对提交数据的**是否为空的简单验证**


---

```java
//1. 校验用户是否有效
					//-SysUser为登录帐号(有密码),通过帐号名从数据库中获取整个帐号
		SysUser sysUser = sysUserService.getUserByName(username);
					//-验证用户是否有效(可能该帐号已经注销,冻结,)
		result = sysUserService.checkUserIsEffective(sysUser);
					//-如果不成功就返回对应的结果(显示冻结,不存在或已注销)
		if(!result.isSuccess()) {
			return result;
		}

		//2. 校验用户名或密码是否正确
					//-对密码进行盐加密
		String userpassword = PasswordUtil.encrypt(username, password.trim(), sysUser.getSalt());
					//-从数据库中读取的密码
		String syspassword = sysUser.getPassword();
					//-两者进行比较
		if (!syspassword.equals(userpassword)) {
			result.error500("用户名或密码错误");
			return result;
		}

		//用户登录信息
		userInfo(sysUser, result);
					//-日志中记录登录信息等
		sysBaseAPI.addLog("用户名: " + username + ",登录成功！", CommonConstant.LOG_TYPE_1, null);
		return result;
	}
```
![20200811155054](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200811155054.png)
>对上面代码进行分析：

1.根据获得的用户名从数据库中读取对应的帐号

2.对该帐号进行校验，代码如图，判断是否有效

3.将前端中的密码进行盐加密并与该帐号的密码进行对比

4.用户名和密码正确，进入用户登录信息业务（选择进入的部门）

5.部门选择成功，将登陆信息写入日志中


---

### 登录部门选择功能
前提：帐号密码正确，进入部门选择

```java
private Result<JSONObject> userInfo(SysUser sysUser, Result<JSONObject> result) {
				//登录成功,获取数据库中的帐号和密码
		String syspassword = sysUser.getPassword();
		String username = sysUser.getUsername();
		// 生成token
		String token = JwtUtil.sign(username, syspassword);
		redisUtil.set(CommonConstant.PREFIX_USER_TOKEN + token, token);
		// 设置超时时间
		redisUtil.expire(CommonConstant.PREFIX_USER_TOKEN + token, JwtUtil.EXPIRE_TIME / 1000);

		// 获取用户部门信息
		JSONObject obj = new JSONObject();
					//通过登录用户的id来获得它可以选择进入的部门
		List<SysDepart> departs = sysDepartService.queryUserDeparts(sysUser.getId());
					//将获取的部门放入departs中,
		obj.put("departs", departs);
		if (departs == null || departs.size() == 0) {
			obj.put("multi_depart", 0);
		} else if (departs.size() == 1) {
						//如果只有一个部门,就获取它的部门编码
			sysUserService.updateUserDepart(username, departs.get(0).getOrgCode());
			obj.put("multi_depart", 1);
		} else {
			obj.put("multi_depart", 2);
		}
		obj.put("token", token);
		obj.put("userInfo", sysUser);
		result.setResult(obj);
		result.success("登录成功");
		return result;
	}
```
>流程

1.获取正确的帐号密码，生成token，设置redis的失效时间

2.获取即将登录成功的帐号相关联的部门信息

3.将用户信息及其部门信息放入object中并返回给帐号密码验证功能

4.帐号密码验证功能成功返回登录信息



## shiro


### 获取用户的权限和角色信息
```java
// 设置用户拥有的角色集合，比如“admin,test”
		//获取role_code集合
		Set<String> roleSet = sysUserService.getUserRolesSet(username);
					//将角色放入权限中
		info.setRoles(roleSet);

		// 设置用户拥有的权限集合，比如“sys:role:add,sys:user:add”
		Set<String> permissionSet = sysUserService.getUserPermissionsSet(username);
		info.addStringPermissions(permissionSet);
		return info;
```



`public Set<String> getUserRolesSet(String username)`
获取用户角色的具体操作

```java
@Override
	@Cacheable(value = CacheConstant.LOGIN_USER_RULES_CACHE,key = "'Roles_'+#username")
	public Set<String> getUserRolesSet(String username) {
		// 查询用户拥有的角色集合
		List<String> roles = sysUserRoleMapper.getRoleByUserName(username);
		log.info("-------通过数据库读取用户拥有的角色Rules------username： " + username + ",Roles size: " + (roles == null ? 0 : roles.size()));
		return new HashSet<>(roles);
	}

	/*通过user_role获取用户的role_code*/
	@Select("select role_code from sys_role where id in (select role_id from sys_user_role where user_id = (select id from sys_user where username=#{username}))")
	List<String> getRoleByUserName(@Param("username") String username);
```



`public Set<String> getUserPermissionsSet(String username)`
获取用户的权限,写入Set并返回

```java
/**
	 * 通过用户名获取用户权限集合
	 *
	 * @param username 用户名
	 * @return 权限集合
	 */
	@Override
	@Cacheable(value = CacheConstant.LOGIN_USER_RULES_CACHE,key = "'Permissions_'+#username")
	public Set<String> getUserPermissionsSet(String username) {
		Set<String> permissionSet = new HashSet<>();
		List<SysPermission> permissionList = sysPermissionMapper.queryByUser(username);
		for (SysPermission po : permissionList) {
			if (oConvertUtils.isNotEmpty(po.getPerms())) {
				//将权限写入集合
				permissionSet.add(po.getPerms());
			}
		}
		log.info("-------通过数据库读取用户拥有的权限Perms------username： "+ username+",Perms size: "+ (permissionSet==null?0:permissionSet.size()) );
		return permissionSet;
	}

	

	<!-- 获取登录用户拥有的权限 -->
				<!--通过role_permission,user_role,来获得用户拥有的permission-->
	<select id="queryByUser" parameterType="Object"  resultMap="SysPermission">
		   SELECT p.*
		   FROM  sys_permission p
		   WHERE exists(
		   		select a.id from sys_role_permission a
		   		join sys_role b on a.role_id = b.id
		   		join sys_user_role c on c.role_id = b.id
		   		join sys_user d on d.id = c.user_id
		   		where p.id = a.permission_id AND d.username = #{username,jdbcType=VARCHAR}
		   )
		   and p.del_flag = 0
		   order by p.sort_no ASC
	</select>

```

### 通过token获取权限

**通过token获取用户的所有权限和菜单**

获取当前token的用户能访问的菜单权限集合，集合中是否有首页菜单，没有就创建一个首页（内容为实体类默认内容）,获取用户的菜单JSON数组，权限JSON数组和 有效按钮权限数组,将所有数组存入json并返回
```java
/**
	 * 查询用户拥有的菜单权限和按钮权限（根据TOKEN）
	 *
	 * @return
	 */
	
	@RequestMapping(value = "/getUserPermissionByToken", method = RequestMethod.GET)
	public Result<?> getUserPermissionByToken(@RequestParam(name = "token", required = true) String token) {
		Result<JSONObject> result = new Result<JSONObject>();
		try {
			if (oConvertUtils.isEmpty(token)) {
				return Result.error("TOKEN不允许为空！");
			}
			log.info(" ------ 通过令牌获取用户拥有的访问菜单 ---- TOKEN ------ " + token);
			String username = JwtUtil.getUsername(token);
						//获取登录用户 的权限permission集合
			List<SysPermission> metaList = sysPermissionService.queryByUser(username);
						//如果没有首页，就会新建一个首页
			PermissionDataUtil.addIndexPage(metaList);
			JSONObject json = new JSONObject();
			JSONArray menujsonArray = new JSONArray();
						//获取菜单JSON数组，将菜单数据存入menujsonArray
			this.getPermissionJsonArray(menujsonArray, metaList, null);
			JSONArray authjsonArray = new JSONArray();
						//获取权限JSON数组，即perms的值和类型存入authjsonArray
			this.getAuthJsonArray(authjsonArray, metaList);
			//查询所有的权限
			LambdaQueryWrapper<SysPermission> query = new LambdaQueryWrapper<SysPermission>();
						//查询del_flag=0和menu是按钮  的菜单权限，存入query
			query.eq(SysPermission::getDelFlag, CommonConstant.DEL_FLAG_0);
			query.eq(SysPermission::getMenuType, CommonConstant.MENU_TYPE_2);
			//query.eq(SysPermission::getStatus, "1");
						//将元组转化为集合allAuthList
			List<SysPermission> allAuthList = sysPermissionService.list(query);
			JSONArray allauthjsonArray = new JSONArray();
			this.getAllAuthJsonArray(allauthjsonArray, allAuthList);
					//json获得了所有permission的信息
			json.put("menu", menujsonArray);
			json.put("auth", authjsonArray);
			json.put("allAuth", allauthjsonArray);
			result.setResult(json);
			result.success("查询成功");
		} catch (Exception e) {
			result.error500("查询失败:" + e.getMessage());
			log.error(e.getMessage(), e);
		}
		return result;
	}
```
**获取菜单JSON数组**

将metaList集合中的所有菜单权限按顺序以json类型（将菜单权限的信息存入）放入jsonArray中并设置菜单的顺序（子菜单依靠父菜单，子按钮放入父菜单）
```java
/**
	  *  获取菜单JSON数组
	 * @param jsonArray
	 * @param metaList
	 * @param parentJson
	 */
			//将metaList（菜单集合）根据父子节点的结构将菜单信息放入jsonArray中
	private void getPermissionJsonArray(JSONArray jsonArray, List<SysPermission> metaList, JSONObject parentJson) {
		for (SysPermission permission : metaList) {
					//菜单没有类型直接跳过
			/*
			* 上半部分用于存放permission数据，
			* 如果该权限没有菜单类型，跳过  permission空，跳过
			* 								1.如果没有父权限，就将该权限放入权限集合
			* 								2.如果有父权限，就会递归，先将父权限放入
			*			如果该权限下面还有权限（子权限），就递归
			*
			*
			* */
			if (permission.getMenuType() == null) {
				continue;
			}
					//获取菜单的父菜单
			String tempPid = permission.getParentId();
					//将菜单信息存放到json中
			JSONObject json = getPermissionJsonObject(permission);
			if(json==null) {
				continue;
			}
					//没有副菜单，将该json（前面已经将菜单放入json中）放入数组jsonArray
			if (parentJson == null && oConvertUtils.isEmpty(tempPid)) {
				jsonArray.add(json);

				// 该权限不是叶子节点（代表不是终点，下面还有子节点），递归并将该菜单作为父菜单参数执行
				if (!permission.isLeaf()) {
					getPermissionJsonArray(jsonArray, metaList, json);
				}

				/*

				* 下半部分是对父菜单进行操作，
				* 1.当前菜单是 ‘按钮’，就将其放入 ‘父菜单的标签’ 的 ‘permissionList’ 中
				* 2.当前菜单是 ‘一级或子菜单’，就将放入 ‘父菜单’ 的 ‘children’ 中
				*
				* */
						//参数中的父菜单能够正确匹配当前菜单的父菜单
			} else if (parentJson != null && oConvertUtils.isNotEmpty(tempPid) && tempPid.equals(parentJson.getString("id"))) {
				// 类型( 0：一级菜单 1：子菜单 2：按钮 )
							/*
								当前菜单是按钮，那么其父菜单是一级或子菜单，将按钮放入父菜单的标签中的permissionList

							* 当前菜单为按钮时，那么获得其父菜单的标签信息，
							* 1.如果父菜单标签第一次出现，就在标签中创建一个permissionList用来存放子菜单的信息（json）
							* 2.如果不是第一次出现，那么已经有permissionList，就直接添加进去子菜单的信息
							*
							* */
									//如果该权限的菜单类型为按钮，获取父菜单的标签（在上半部分的递归中已经将父菜单的信息存入json数组中）
				if (permission.getMenuType().equals(CommonConstant.MENU_TYPE_2)) {
					JSONObject metaJson = parentJson.getJSONObject("meta");
									//如果父菜单标签拥有permissionList字段，就将该菜单放入父菜单标签的permissionList
					if (metaJson.containsKey("permissionList")) {
						metaJson.getJSONArray("permissionList").add(json);
					} else {			//没有就设置一个permissionList字段存放父菜单标签下的子权限（相当于父权限第一次出现时创建，后面再次出现时已经创建，只需添加即可）
						JSONArray permissionList = new JSONArray();
						permissionList.add(json);
						metaJson.put("permissionList", permissionList);
					}
					// 类型( 0：一级菜单 1：子菜单 2：按钮 )

							/*
							当前权限是菜单，就将当前权限放入父菜单的下级中（children）

							* 当前菜单为一级或子菜单，
							* 1.父菜单第一次出现，创建children字段，并将子菜单放入children
							* 2.不是第一次出现，将子菜单信息放入children
							*
							* */
									//如果当前菜单的菜单类型为 子菜单 或 一级菜单
				} else if (permission.getMenuType().equals(CommonConstant.MENU_TYPE_1) || permission.getMenuType().equals(CommonConstant.MENU_TYPE_0)) {
									//父菜单如果有children字段，就将子菜单存入
					if (parentJson.containsKey("children")) {
						parentJson.getJSONArray("children").add(json);
					} else {		//父菜单没有该字段，创建并添加。
						JSONArray children = new JSONArray();
						children.add(json);
						parentJson.put("children", children);
					}

					// 该权限不是叶子节点（代表不是终点，下面还有子节点），递归并将该菜单作为父菜单参数执行
					if (!permission.isLeaf()) {
						getPermissionJsonArray(jsonArray, metaList, json);
					}
				}
			}

		}
	}
```

## @RequestBody
 用于接受请求体中的数据,并将其放置到指定的变量中

### 1.将前端数据放置到jsonString
```java
@RequestMapping("/test1")
public String void toJson(@RequestBody String jsonString){
    System.out.print(jsonString);
    return jsonString;
}
```
**使用Swagger测试**
- 传入数据
![20200811114809](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200811114809.png)
- 传出数据
![20200811114908](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200811114908.png)
- 项目控制台
![20200811114845](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200811114845.png)
前端传来的json数据（k-v），后端只有接受value数据。

### 2.将前端数据放置到实体类
设置一个String name和int age的实体类
```java
@PostMapping("/test2")
    public String u(@RequestBody Ser user){
        System.out.println(user.toString());
        return user.toString();
    }
```
- 传入数据
![20200811144839](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200811144839.png)
- 传出数据
![20200811144913](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200811144913.png)

### 3.与@RequsetParam结合
```java

```