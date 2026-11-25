---
title: MyBatis-Plus 学习笔记
date: 2026-11-25 18:04:00
tags:
  - MyBatis-Plus
categories:
  - dev
author: pairs
top: false
cover: /images/default-cover.jpg
toc: true
---

# MyBatisPlus

### 1.Mybatis+MP实现查询user

**第一步，将UserMapper继承BaseMapper，将拥有了BaseMapper中的所有方法：**

```
public interface UserMapper extends BaseMapper<User>{

} 
```

**第二步，使用MP中的MybatisSqlSessionFactoryBuilder进程构建：**

```
public class TestMybatsiPlus{

	@Test
	public void testUserList() throws Exception{
		String resource = "mybatis=config.xml";
		InputStream inputStream = Resources.getResourceAsStream(resource);
		//使用Mp的mybatisSqlSessionFactoryBuilder
		SqlSessionFactory sqlSessionFactory = new MybatisSqlSessionFactoryBuilder().build(inputStream);
		SqlSession sqlSession = SqlSessionFactory.openSession();
		
		UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
		//使用BaseMapper中定义的方法
		List<User> list = userMapper.selectList(null);
		for(User user : list){
			System.out.println(user);
		}
	}
}
```

**注意：若报表名错误，在User类中添加@TableName，指定数据库表名**

### 2.Spring+Mybatis+MP

引入了Spring框架，数据源，构建等工作就交给Spring管理

1.创建子Module

导入Spring相关依赖

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/mybatisplus/image-20220210144935942.png" alt="image-20220210144935942" style="zoom:67%;" />

2.编写jdbc.properties

3.编写applicationContext.xml

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/mybatisplus/image-20220210145210317.png" alt="image-20220210145210317" style="zoom:67%;" />

4.编写User对象类以及UserMapper接口

5.编写测试用例

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/mybatisplus/image-20220210145615398.png" alt="image-20220210145615398" style="zoom:67%;" />

### 3.通用CRUD

#### 3.1插入操作

@TableId

用在id上，让id实现自动增长

```
@TableId(type = IdType.AUTO)

private Long id;
```

@TableField

在MP中通过@TableField注解可以指定字段的一些属性，常常解决的问题有两个：

1.对象中的属性名和字段名不一致的问题(非驼峰)

2.对象中的属性字段在表中不存在的问题

```
@TableField(value = "email")   //指定数据表中字段名
private String mail;

@TableField(exist = false)   //在数据库表中不存在
private String address;

@TableField(select = false)   //查询时不返回该字段的值
private String password;
```

#### 3.2更新操作

在MP中，更新操作有2种，一种是根据id更新，另一种是根据条件跟新。

##### 3.2.1根据id更新

updateById(User user)

##### 3.2.2根据条件更新

引用QueryWrapper

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/mybatisplus/image-20220210155311664.png" alt="image-20220210155311664" style="zoom:67%;" />

引用UpdateWrapper

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/mybatisplus/image-20220210155524616.png" alt="image-20220210155524616" style="zoom:67%;" />

#### 3.3删除操作

##### 3.3.1deleteById

##### 3.3.2deleteByMap

```
/**
	*根据columnMap条件，删除纪录
	*
	*@param columnMap表字段map对象
	*/
	int deleteByMap(@Param(Constants.COLUMN_MAP)Map<String,Object> columnMap);
```

##### 3.3.3delete

```
//根据entity条件，删除纪录
//@param wrapper 实体对象封装操作类(可以为null)
int delete(@Param(Constants.WRAPPER) Wrapper<T> wrapper);
```

##### 3.3.4deleteBatchIds

```
//删除(根据id 批量删除)
//@Param idList 主键ID列表(不能为 null 以及 enpty)
int deleteBatchIds(@Param(Constants.COLLECTION) Collection<? extends Serializable> idList);

//例
public void DeleteBatchIds(){
	int result = this.userMapper.deleteBatchIds(Arrays.asList(10,11));
}
```

#### 3.4查询操作

##### 3.4.1selectById

##### 3.4.2selectBatchIds (同批量删除)

##### 3.4.3selectOne

```
//根据entity条件，查询一条记录
//@param queryWrapper 实体对象封装操作类(可以为null)

Integer selectOne(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);
```

**注意：查询结果超过1条会报错**

##### 3.4.4selectCount

查询数据条数

```
T selectCount(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);
```

##### 3.4.5selectList

##### 3.4.6selectPage

```
//根据entity条件，查询全部纪录(并翻页)
//@Param page 	分页查询条件(可以为 RowBounds.DEFAULT)
//@Param queryWrapper   实体对象封装操作类(可以为 null)

IPage<T> selectPage(IPage<T> page,@Param(Constants.WRAPPER) WRapper<T> queryWrapper);
```

配置分页插件：MPConfig

创建配置类MPConfig

```
@Configuration
public class MPConfig{
	
	@Bean	//配置分页插件
	public PaginationInterceptor paginationInterceptor(){
		return new PaginationInterceptior();
	}
}
```

测试类：

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/mybatisplus/image-20220210182908867.png" alt="image-20220210182908867" style="zoom:67%;" />

#### 3.5SQL注入原理

### 4.配置

在MP种有大量的配置，其中一部分是Mybatis原生的配置，另一部分是MP的配置，详情：https://mybatis.plus/comfig/

#### 4.1基本配置

##### 4.1.1configLocation

MyBatis配置文件位置，如果有单独的MyBatis配置，要将路径配置到configLocation中。MyBatisConfiguration的具体内容参考MyBatis官方文档。

SpringBoot：

```
mybatis-plus.config-location = classpath:mybatis-config.xml
```

SpringMVC:

![image-20220211135016266](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/mybatisplus/image-20220211135016266.png)

##### 4.1.2mapperLocations

MyBatis Mapper 所对应的XML文件位置，如果在Mapper中有自定义方法(XML 中有自定义实现),需要进行该配置，告诉Mapper所对应的XML文件位置。

SpringBoot：

```
mybatis-plus.mapper-locations = classpath*:mybatis/*.xml
```

SpringMVC:

![image-20220211135521011](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/mybatisplus/image-20220211135521011.png)

##### 4.1.3typeAliasesPackage

MyBatis别名扫描路径，通过该属性可以给包中的类注册别名，注册后在Mapper对应得XML文件中可以直接使用类名，而不适用全限定得类名(即XML 中调用的时候不用包含包名)。

SpringBoot：

```
mybatis-plus.type-aliases-package = cn.itchast.mp.pojo
```

SpringMVC:

![image-20220211140145362](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/mybatisplus/image-20220211140145362.png)

#### 4.2进阶配置

本部分的配置大部分为MyBatis原生支持的配置，这意味着可以通过MyBatis  XML配置文件的形式进行配置

##### 4.2.1mapUnderscoreToCamelCase

+ 类型：boolean
+ 默认值：true

是否开启自动驼峰命名规则(camel case)映射，即从经典数据库名A_COLUMN(下划线命名)到经典java属性名aColumn(驼峰命名)的类型映射。

**注意：此属性在MyBatis中默认值为false，**

​			**如果数据库命名符合规则无需使用@TableField注解指定数据库字段名**

SpringBoot：

```
#关闭自动驼峰映射，该参数不能和mybatis-plus.config-location同时存在
mybatis-plus.configuration.map-underscore-to-camen-case=fasle
```

##### 4.2.2cacheEnabled

+ 类型：boolean
+ 默认值：true

全局开启或关闭配置文件中的所有映射器已经配置的任何缓存。

示例：mybatis-plus.configuration.cache-enable=fasle

#### 4.3DB策略配置

##### 4.3.1idType

+ 类型：com.baomidou.mybatisplus.annotation.IdType
+ 默认值：ID_WORKER

全局按默认主键类型，设置后，即可省略实体对象中的@Table(type=IdType.AUTO)配置。

实例：

SpringBoot：

```
mybatis-plus.global-config.db-config.id-type=auto
```

SpringMVC:

![image-20220211144459685](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/mybatisplus/image-20220211144459685.png)

##### 4.3.2tablePrefix

+ 类型：String
+ 默认值：null

表名前缀，全局配置后可省略@TableName()配置。

SpringBoot：

```
mybatis-plus.golbal-config.db-config.table-prefix=tb_
```

SpringMVC:

![image-20220211144944680](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/mybatisplus/image-20220211144944680.png)

### 5.条件构造器

在MP中，Wrapper接口的实现类关系如下：

![image-20220211145123182](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/mybatisplus/image-20220211145123182.png)

**说明：QueryWrapper(LambdaQueryWrapper)和UpdateWrapper(LambdaUpdateWrapper)的父类用于生成sql的where条件，entity属性也可以用于生成sql的where条件**

**注意：entity生成的where条件与使用各个api生成的where条件没有任何关联行为**

#### 5.1allEq

##### 5.1.1说明

```
allEq(Map<R,V> params)
allEq(Map<R,V> params,boolena null2IsNull)
allEq(boolean condition,Map<R,V> params,boolean null2IsNull)
```

+ 全部eq(或个别isNull)

```
个别参数说明：
params:key为数据库字段名，value为字段值
null2IsNull：为true则在map的value为null时调用isNull方法，为false时则忽略value为null
例1：allEq({id:1,name:"老王",age:null}) --->id=1 and name="老王" and age is null
例2：allEq({id:1,name:"老王",age:null}) --->id=1 and name="老王"
```

#### 5.2基本比较操作

+ eq：等于 = 
+ ne：不等于 <>
+ gt：大于  >
+ ge：大于等于   >=
+ lt：小于   <
+ le：小于等于   <=
+ between：BETWEEN 值1 AND 值2
+ notBetween：NOT BETWEEN 值1 AND 值2
+ in：字段IN(value.get(0),value.get(1),...)
+ notIn：字段 NOT IN(v0,v1,...)

#### 5.3模糊查询

+ like
  + LIKE '%值%'
  + 例：like("name","王")   --->name like '%王%'
+ notLike
  + NOT LIKE '%值%'
  + 例：notLike("name","王")   --->name not like '%王%'
+ likeLeft
  + LIKE '%值'
  + 例：likeLeft("name","王")    ---->name like '%王'
+ likeRight
  + Like '值%'
  + 例：likeRight("name","王")   --->name like '王%'

#### 5.4排序

+ orderBy
  + 排序：ORDER BY 字段,...
  + 例：orderBy(true,true,"id","name")  --->order by id ASC,name ASC
+ orderByAsc
  + 排序：OEDER BY 字段,...ASC
  + 例：oederByAsc("id","name")   --->order by id ASC,name ASC
+ orderByDesc
  + 排序：ORDER By 字段,...DESC
  + 例：orderByDesc("id","name")   --->order by id DESC,name DESC

#### 5.5逻辑查询

+ or
  + 拼接 OR
  + 主动调用or表示紧接着下一个方法不是用and连接(不调用or则默认使用and连接)
+ and
  + AND  嵌套
  + 例：and(i -> i.eq("name","李白").ne("status","活着"))    --->and (name = '李白' and status <> '活着')

#### 5.6Select

**在MP查询中，默认查询所有的字段，如果有需要可以通过select()方法进行指定字段。**

![image-20220211153207218](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/mybatisplus/image-20220211153207218.png)

### 6.ActiveRecord

ActiveRecord也属于ORM(对象关系映射)层，由Rails最早提出，遵循标准的ORM模型：表映射到纪录，纪录映射到对象，字段映射到对象属性，配合遵循的命名和配置惯例，能够很大程度的快速实现模型的操作，而且简洁易懂。

ActiceRecord的主要思想是：

+ 每一个数据库表对应创建一个类，类的每一个对象实例对应于数据库中表的一行纪录，通常表的每个字段在类中都有相应的Field；
+ ActiveRecord同时负责把自己持久化，在ActiveRecord中封装了对数据库的访问，即CRUD；
+ ActiveRecord是一种领域模型(Domain Model)，封装了部分业务逻辑；

#### 6.1AR开始

在MP中，开启AR非常简单，只需要将实体类对象继承Model<T>即可。

#### 6.2根据主键查询

测试用例：

```
@RunWith(SpringRunner.class)
@SpringBootTest
public class test{

	@AutoWired
	private UserMapper userMapper;
	
	@Test
	public void test1(){
		User user = new User();
		user.setId(2);
		User user1 = user.selectById();
		
		System.out.println(user1);
	}
}
```

#### 6.3新增数据

#### 6.4更新操作

#### 6.5删除操作

#### 6.6根据条件查询

![image-20220212145524100](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/mybatisplus/image-20220212145524100.png)

### 7.Oracle主键Sequence

在mysql中，主键往往是自增长的，但如果使用的是Oracle数据库，那么就不能使用自动增长，就得使用Sequence序列生成id值。

#### 7.1部署Oracle环境

#### 7.2创建表以及序列

![image-20220212165349454](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/mybatisplus/image-20220212165349454.png)

#### 7.3jdbc驱动包

由于版权原因，我们不能直接通过maven的中央仓库下载oracle数据库的jdbc驱动包，所以我们需要将驱动包安装到本地仓库。

![image-20220212165533916](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/mybatisplus/image-20220212165533916.png)

#### 7.4修改application.properties

![image-20220212165607101](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/mybatisplus/image-20220212165607101.png)

#### 7.5配置序列

使用Oracle的序列需要做两件事：

第一，需要配置MP的序列生成器到Spring容器：即MPConfig

![image-20220212165859011](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/mybatisplus/image-20220212165859011.png)

第二，在实体类对象中指定序列的名称：

```java
@keySequence(value = "SEQ_USER",clazz = Long.class)
public class User{
    ......
}
```

#### 7.6测试

### 8.插件

#### 8.1mubatis的插件机制

MyBatis允许在已映射语句执行过程中的某一点进行拦截调用，默认情况下，MyBatis允许使用插件来拦截的方法调用包括：

1.Executor(update,query,flushStatements,commit,rollback,getTransaction,close,isClosed)

2.ParameterHandler(getParameterObject,setParameters)

3.ResultSetHandler(handlerResultSets,handlerOutputParameters)

4.StatementHandler(perpare,parameterize,batch,update,query)

我们看到了可以拦截Executor接口的部分方法，比如update，query，commit，rollback等方法，还有其他接口的一些方法等。

总体概括为：

1.拦截执行器的方法

2.拦截参数的处理

3.拦截结果集的处理

4.拦截Sql语法构建的处理

#### 8.2执行分析插件

在MP中提供了对SQL执行的分析的插件，可用作阻断全表更新，删除的操作

**注意：该插件仅适用于开发环境，不适用于生产环境。**

SpringBoot配置：在MPConfig中进行配置(或者在mybatis-config.xml中进行配置也可以)

```java
@Bean
public SqlExplainInterceptor sqlExplainInterceptor(){
    SqlExplainInterceptior sqlExplainInterceptior = new SqlExplainInterceptor();
    List<ISqlParser> sqlParserList = new ArrayList<>();
    //攻击QL阻断解析器，加入解析链
    sqlParserList.add(new BolckAttackSqlParser());
    
    SqlExplainInterceptor.setSqlPaeserList(sqlParserList);
    
    return sqlExplainInterceptor;
}
```

#### 8.3性能分析插件

性能分析拦截器，用于输出每条SQL语句及其执行时间，可以设置最大执行时间，超过时间会抛出异常。

**注意：该插件只用于开发环境，不建议生产环境使用**

在mybatis-config.xml中进行配置(或者在SpringBoot中的MPconfig类中做配置都可以)

```xml
<plugins>
	<!--性能分析插件-->
    <plugin interceptor="com.baomidou.mybatisplus.extension.plugins.PerformanceInterceptor">
        <!--最大执行时间，单位为ms-->
    	<property name="maxTime" value="100"/>
        <!--对输出的SQL做格式化，默认为false-->
        <property name="format" value="true"/>
    </plugin>
</plugins>

```

#### 8.4乐观锁插件

##### 8.4.1主要使用场景

意图：

当要更新一条纪录的时候，希望这条纪录没有被别人更新

乐观锁实现方式：

+ 取出记录时，获取当前的version
+ 更新时，带上这个version
+ 执行更新时，set version = newVersion where version = oldVersion
+ 如果version不对，就更新失败

##### 8.4.2插件配置(3中方式)

spring xml:

```xml
<bean class="com.baomidou.mybatisplus.extension.plugins.OptimisticLockerInterceptor"/>
```

SpringBoot:

```java
@Bean
public OptimisticLockerInterceptor optimisticLockerInterceptor(){
    return new OptimisticLockerInterceptor();
}
```

mybatis-confing.xml:

```xml
<pligin interceptor="com.baomidou.mybatisplus.extension.plugins.OptimisticLockerInterceptor"></pligin>
```

##### 8.4.3注解实体字段

需要为实体字段添加@Version注解

![image-20220213133111454](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/mybatisplus/image-20220213133111454.png)

第一步，为表添加version字段，并且设置初始值为1。

第二步，为User实体对象添加version字段，并且添加@Version注解

##### 8.4.4测试

测试用例:

```java
@Test
public void testUpdate(){
    User user = new User();
    user.setAge(20);
    user.setId(2);
    user.setVersion(1);//获取到Version为1
    
    int result = this.UserMapper.updateById(user);
    System.out.println("reslult=>" + result);
}
```

##### 8.4.5特别说明

+ **支持的数据类型只有：int,Integer,long,Long,Date,Timestamp,LocalDateTime**
+ 整数类型下 newVersion = oldVersion + 1
+ newVersion 会回写到entity中
+ 仅支持updateById(id) 与 update(entity,wrapper) 方法
+ **在update(entity,wrapper)方法下，wrapper不能复用！！！**

### 9.Sql注入器

在MP中，通过AbstractSqlInjector将BaseMapper中的方法注入到了Mybatis容器，这样这些方法才可以正常执行。

扩充BaseMapper中的方法：

例：扩充findAll()方法

#### 9.1编写MyBaseMapper

```java
public interface MyBaseMapper<T> extends BaseMapper<T>{
	
	List<T> findAll();
    //扩展其他方法
}
```

**后续中所有的Mapper继承不继承BaseMapper，而是继承我们自己写的MyBaseMapper。**

#### 9.2编写MySqlInjector

*如果直接继承AbstractSqlInjector的话，原有的BaseMapper中的方法将失效，所以我们选择继承DefaultSqlInjector进行扩展。*

创建injectors包，

创建MySqlInjector类 ，进行编写

```java
public class MySqlInjector extends DefaultSqlInjector{

	@Override
	public List<AbstractMethod> getMethodList(){
		List<AbstractMethod> list = new ArrayList<>();
		
		//获取父类中的集合
		list.addAll(super.getMethodList());
		//再扩充自定义的方法
		list.add(new FindAll());
        
        return list;
	}
}
```

#### 9.3创建FindAll类，进行编写

```java
public class FindAll extends AbstractMethod{

	@Override
	public MappedStatement injectMappedStatement(Class<?> mapperClass,Class<?> modelClass,TableInfo tableInfo){
		String sqlMethod = "findAll";
		String sql = "select * from " + tableInfo.getTableName();
		sqlSource sqlSource = languageDriver.createSqlSource(configuration,sql,modelClass);
		return this.addSelectMappedStatement(mapperClass,sqlMethod,sqlSource,modelClass,tableInfo);
	}
}
```

#### 9.4将SqlInjector注入到容器中    (MPConfig类中进行操作)

```java
//自定义SQL注入器
@Bean
public MySqlInjector mySqlInjector(){
	return new MySqlInjector();
}
```

#### 9.5测试

```java
@Test
public void testFindAll(){
	List<User> users = this.userMapper.findAll();
}
```

### 10.自动填充功能

插入或者更新数据时，希望有些字段可以自动填充，比如，密码，version等，MP中提供了这样的功能，可以实现自动填充。

#### 10.1添加@TableField注解

```java
@TableField(fill = FieldFill.INSERT)//插入数据时进行填充
private String password;
```

为password添加自动填充功能，在新增数据时有效。

FieldFill提供了多种模式选择：

![image-20220213143827127](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/mybatisplus/image-20220213143827127.png)

#### 10.2编写MyMetaObjectHandler

创建一个handler包，在包中创建MyMetaObjectHander类

```java
@Component
public class MyMetaObjectHander implements MetaObjectHander{
    //插入数据时进行填充
    @Override
    public void insertFill(MetaObject metaObject){
        //获取到password的值，进行判断是否为空，进行操作
        Object password = getFieldValByName("password",metaObject);
        if( null == password){
            setFieldValByName("password","888888",metaObject);
        }
    }
    //更新数据时进行填充
    @Override
    public void updateFill(MetaObject metaObjcet){
        
    }
}
```

### 11.逻辑删除

开发系统时，有时候在实现功能时，删除操作需要实现逻辑删除，就是将数据标记为删除，而并非真正的物理删除(非DELETE操作),查询时需要携带状态条件，确保被标记的数据不被查询到，这样的目的就是为了避免数据被真正的删除。MP提供了这样的功能。

#### 11.1修改表结构

为tb_user表添加deleted字段，用于表示数据是否被删除，1代表删除，0代表未删除。

同时，修改User实体，增加deleted属性并且添加@TableLogic注解

#### 11.2配置

```properties
# 逻辑已删除值(默认为1)
mybatis-plus.global-config.db-config.logic-delete-value=1
# 逻辑未删除值(默认为0)
mybatis-plus.global-config.db-config.logic-not-delete-value=0
```

#### 11.3测试

```java
@Test
public void testdeleteById(){
	this.userMapper.deleteById(2);
}
```

执行的SQL：

![image-20220213155510003](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/mybatisplus/image-20220213155510003.png)

### 12.通用枚举

解决了繁琐的配置，让mybatis优雅的使用枚举属性

#### 12.1修改表结构

增加一个性别字段

![image-20220213155731346](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/mybatisplus/image-20220213155731346.png)

#### 12.2定义枚举

创建枚举包enums,在包中创建类，实现IEnum接口

```java
public enum SexEnum implements IEnum<Integer>{
    
    MAN(1,"男"),
    WOMAN(2,"女");
    
    private int value;
    private String desc;
    
    SexEnum(int value,String desc){
        this.value = value;
        this.desc = desc;
    }
    
    @Override
    public Integer getValue(){ return this.value; }
    
    @Override
    public String toString(){ return this.desc; }
}
```

#### 12.3配置

```
#枚举包扫描
mubatis-plus.type-enums-package=cn.itcast.mp.enums
```

#### 12.4修改实体

在实体类中加入SexEnum属性

private SexEnum sex;

#### 12.5测试

插入，查询中枚举都是有效的。

![image-20220213161201033](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/mybatisplus/image-20220213161201033.png)

### 13.代码生成器

AutoGenerator是MyBatis-Plus的代码生成器，通过AutoGenerator可以快速生成Entity,Mapper,MapperXML,Service,Controller等各个模块的代码，极大的提升了开发效率。

### 14.MybatisX快速开发插件

MybatisX是一款基于IDEA的快速开发插件，为效率而生

安装方法：打开IDEA，进入File->Settings->Plugins->Browse Repositories，输入mubatisx搜索并安装。

功能：

+ Java与XML调回跳转
+ Mapper方法自动生成XML

