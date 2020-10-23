

# 流程

> a. SpringBootApplication 负责扫描自己和依赖的包下的对象实例，不然 @Autowired 报错
> b. MapperScan 负责自己和依赖的 Mybatis 的 DAO 接口的注册
> c. application.properties 的 mapperLocations 则是说明所有应用类加载路径下的 mapper 文件都需要注册
> d. 各被依赖的 jar (springboot)工程负责实例化自己的 mapper xml 实例



## 1.生成表

在mybatis generator项目中的generatorConfig文件中**设置想要逆向生成的表**

```xml
<table schema="" tableName="bus_car" domainObjectName="BusCar" enableCountByExample="true" enableUpdateByExample="true" enableDeleteByExample="true" enableSelectByExample="true" selectByExampleQueryId="false">

		</table>
			<!--消息-->
		<table schema="" tableName="bus_message" domainObjectName="BusMessage" enableCountByExample="true" enableUpdateByExample="true" enableDeleteByExample="true" enableSelectByExample="true" selectByExampleQueryId="false">

		</table>
			<!--路线-->
		<table schema="" tableName="bus_route" domainObjectName="BusRoute" enableCountByExample="true" enableUpdateByExample="true" enableDeleteByExample="true" enableSelectByExample="true" selectByExampleQueryId="false">

		</table>
			<!--高频路线日汇总-->
		<table schema="" tableName="bus_route_high_sell_day" domainObjectName="BusRouteHighSellDay" enableCountByExample="true" enableUpdateByExample="true" enableDeleteByExample="true" enableSelectByExample="true" selectByExampleQueryId="false">

		</table>
			<!--高频路线月汇总-->
		<table schema="" tableName="bus_route_high_sell_month" domainObjectName="BusRouteHighSellMonth" enableCountByExample="true" enableUpdateByExample="true" enableDeleteByExample="true" enableSelectByExample="true" selectByExampleQueryId="false">

		</table>
			<!--安检统计-->
		<table schema="" tableName="bus_security_check" domainObjectName="BusSecurityCheck" enableCountByExample="true" enableUpdateByExample="true" enableDeleteByExample="true" enableSelectByExample="true" selectByExampleQueryId="false">

		</table>
			<!--售票统计-->
		<table schema="" tableName="bus_sell" domainObjectName="BusSell" enableCountByExample="true" enableUpdateByExample="true" enableDeleteByExample="true" enableSelectByExample="true" selectByExampleQueryId="false">

		</table>
```



## 2.增加字段

2.1想要附带显示busStation的车站名称和隶属关系，就需要在实体类中添加字段

```java
 private String stationName;

  private String Relation;
//省略getter和setter方法
```

2.2在mapper映射文件中同样**添加相应的字段**，同时可能会产生**冲突字段**，需要**添加前缀表名**。

```xml
//添加相应的字段
<result column="c.name" jdbcType="VARCHAR" property="stationName"/>
    <result column="relation" jdbcType="VARCHAR" property="relation"/>

//解决冲突字段，添加前缀表名
<sql id="Base_Column_List">
    a.id, bus_station_id, number, brand, type, level, a.del_flag,people_number, c.name,relation
  </sql>

```



## 3.执行查询业务

```java
@Service
public class BusCarService {

    @Resource
    BusCarMapper mapper;

    //全部查询
    public List< BusCar > selectAll(){
        BusCar busCar = new BusCar();
        List< BusCar > carList = mapper.getCarList(busCar);
        return carList;
    }

    

    
}

```

**查询所有信息**

```java
public List< BusCar > selectAll(){
        BusCar busCar = new BusCar();
        List< BusCar > carList = mapper.getCarList(busCar);
        return carList;
    }
}
```

通过example生成**criteria**，用于**条件查询**

**criteria.and/字段名/条件方法**   在where后面增加检索条件

```java
public List<BusCar> getList(BusCar busCar){
        /*List< BusCar > carList = mapper.getCarList(busCar);
        return carList;*/

        BusCarExample example  = new BusCarExample();
        BusCarExample.Criteria criteria = example.createCriteria();
        if(busCar.getLevel()!=null){
            criteria.andLevelEqualTo(busCar.getLevel());
        }
        if (busCar.getRelation() != null) {
            criteria.andRelationEqualTo(busCar.getRelation());
        }
        if (busCar.getNumber() != null) {
            criteria.andNumberLike(busCar.getNumber());
        }
        if (busCar.getType() != null) {
            criteria.andTypeEqualTo(busCar.getType());
        }
        if (busCar.getBusStationId() != null) {
            criteria.andBusStationIdEqualTo(busCar.getBusStationId());
        }

        List< BusCar > busCars = mapper.selectByExample(example);
        return busCars;
    }
```

**新增的字段是没有criteria的方法**，需要自己手动添加，在example中添加方法

```java
public Criteria andRelationEqualTo(String value){
            addCriterion("relation=",value,"relation");
            return (Criteria)this;
        }
```



## 3.插入

插入可以**直接使用mapper提供的方法**，但是需要注意，**插入的id需要自己生成**，create等字段也是

```java
public int insert(BusCar busCar){
        int insert = mapper.insert(busCar);
        return insert;
    }
```





# 注意



主程序入口扫描**mapper接口**

```java
@MapperScan(basePackages ="com.wocai.keyun.dao.bus")
@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```



打jar包后，主项目引用jar包

service层使用对应的mapper时，不要使用@Autowied，要使用@Resourese



配置文件加上jar包中的mapper文件地址

```properties
mybatis.mapper-locations=classpath*:com/wocai/keyun/mapping/bus/*.xml
```







# 问题



1.**报错**can't find 实体类

在xml文件中使用全限定名，加上包名



2.一对一时，不能将外键变成其他类型，而是增加一个其他类型的字段

- 原来**错误**的方式，把外键的类型改成了busStation

```java
public class BusDriver implements Serializable {
    private String id;
    private BusStation busStationId;
```

```xml
<resultMap id="BaseResultMap" type="com.wocai.keyun.entity.bus.BusDriver">
    <id column="id" jdbcType="VARCHAR" property="id" />
    //将外键改成了busStation类型
    <!--<result column="bus_station_id" jdbcType="VARCHAR" property="busStationId" />-->
    <association  property="busStation" javaType="com.wocai.keyun.entity.bus.BusStation">
      <id column="cid" property="id"/>
      <result column="cname" property="name"/>
      <result column="crelation" property="relation"/>
    </association>
  </resultMap>
```



- 正确方式，保留外键，增加新字段存储busStation类型

```java
public class BusDriver implements Serializable {
    private String id;
    private String busStationId;
    private BusStation busStation;
```

```xml
<resultMap id="BaseResultMap" type="com.wocai.keyun.entity.bus.BusDriver">
    <id column="id" jdbcType="VARCHAR" property="id" />
    //保留了外键，并增加了一个busStation的字段
    <result column="bus_station_id" jdbcType="VARCHAR" property="busStationId" />
    
    <association  property="busStation" javaType="com.wocai.keyun.entity.bus.BusStation">
      <id column="cid" property="id"/>
      <result column="cname" property="name"/>
      <result column="crelation" property="relation"/>
    </association>
  </resultMap>
```



3.**Column 'id' in field list is ambiguous**

左连接的时候id重名

```xml
<sql id="Base_Column_List">
    id, bus_station_id, name, phone, type, drive_time, create_by, create_date, update_by,
    update_date, remarks, del_flag,c.id cid,c.name cname,c.relation crelation
  </sql>
```

两个表中都有id，不知道从哪个表中获取，所以需要指定表

```xml
<sql id="Base_Column_List">
    a.id aid, bus_station_id, a.name aname, phone, type, drive_time, create_by, create_date, update_by,
    update_date, remarks, del_flag,c.id cid,c.name cname,c.relation crelation
  </sql>
```



4.**argument type mismatch**

字段名busStation 在xml中的对应名忘记改回来



5.**Unknown column 'busStationId' in 'where clause'**

在example中，设置的是**数据库字段名**，不是实体类属性名

```
public Criteria andBusStationIdEqualsTo(String value){
            addCriterion("bus_station_id = ",value,"bus_station_id");
            return (Criteria) this;
        }
```



6.打包时报错：**程序包xxx.dao.bus不存在**

1.将wocai-dao项目install打包（install和package的区别就是install将项目打包并存入maven仓库，其他项目可以通过pom依赖直接使用）

2.在要使用的项目中引入maven依赖

```xml
		<dependency>
			<!-- 这三个值就是打包项目的三个值-->
            <groupId>com.wocai.wocai-dao</groupId>
            <artifactId>wocai-dao</artifactId>
            <version>1.0</version>
        </dependency>
```

3.使用install打包

