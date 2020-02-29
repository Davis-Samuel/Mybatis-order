# 1.第一个Mybatis程序

## 第一步：项目：Mybatyis(删除src)，模块：mybatis-01

- 创建Mybatis的maven项目(父项目要把src删掉) 父pom.xml导包 和 子maven项目，不用导包

- **<u>在父，子pom.xml里都需要加入一下代码</u>**

- ```xml
  <!--依赖如下-->
  <dependencies>
  
      <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>8.0.16</version>
      </dependency>
      <!--
       https://mvnrepository.com/artifact/org.mybatis/mybatis
      -->
      <dependency>
          <groupId>org.mybatis</groupId>
          <artifactId>mybatis</artifactId>
          <version>3.5.3</version>
      </dependency>
  
      <!--  https://mvnrepository.com/artifact/junit/junit  -->
      <dependency>
          <groupId>junit</groupId>
          <artifactId>junit</artifactId>
          <version>4.12</version>
          <scope>test</scope>
      </dependency>
  
  </dependencies>         
  
  
  <!--    所有的maven项目都需要加进去这个文件，在父，子pom.xml里都需要加入一下代码   -->   
  <!--在build中配置resources，来防止我们资源导出失败的问题-->
      <build>
          <resources>
  
              <resource>
                  <directory>src/main/resources</directory>
                  <includes>
                      <include>**/*.properties</include>
                      <include>**/*.xml</include>
                  </includes>
                  <filtering>true</filtering>
              </resource>
  
              <resource>
                  <directory>src/main/java</directory>
                  <includes>
                      <include>**/*.properties</include>
                      <include>**/*.xml</include>
                  </includes>
                  <filtering>true</filtering>
              </resource>
  
          </resources>
      </build>
  ```

  

## 第二步

- 根据

[Mybatis文档]: https://mybatis.org/mybatis-3/zh/getting-started.html

- 编写resources资源文件里的mybatis-config.xml核心配置文件

- ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE configuration
          PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-config.dtd">
  
          <!-- configuration核心配置文件 -->
  
  <configuration>
      <environments default="development">
          <environment id="development">
              <transactionManager type="JDBC"/>
              <dataSource type="POOLED">
                  <property name="driver" value="com.mysql.jdbc.Driver"/>
                  <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8&amp;serverTimezone=Asia/Shanghai"/>
                  <property name="username" value="root"/>
                  <property name="password" value="3105311"/>
              </dataSource>
          </environment>
      </environments>
      
      <!--每一个Mapper.xml都需要在mybatis-config.xml核心配置文件中注册-->
      <mappers>
          <mapper resource="com/it/Dao/UserMapper.xml"/>
      </mappers>
      
  </configuration>
  ```

  

## 第三步：

- 在java下创建一个com.it.utils的工具包，根据文档写号工具类

- ```java
  package com.it.utils;
  
  //通过 sqlSessionFactory →→→→拿到→→→→ SqlSession
  
  import org.apache.ibatis.io.Resources;
  import org.apache.ibatis.jdbc.SQL;
  import org.apache.ibatis.session.SqlSession;
  import org.apache.ibatis.session.SqlSessionFactory;
  import org.apache.ibatis.session.SqlSessionFactoryBuilder;
  
  import java.io.IOException;
  import java.io.InputStream;
  
  public class MybaticUtils {
      private static SqlSessionFactory sqlSessionFactory;
  //    使用Mabatis的第一步，获取sqlSessionFactory对象
      static {
          try {
              String resource = "mybatis-config.xml";
              InputStream inputStream = Resources.getResourceAsStream(resource);
              sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
          } catch (IOException e) {
              e.printStackTrace();
          }
      }
  
  //    既然有了 SqlSessionFactory，顾名思义，我们就可以从中获得 SqlSession 的实例了。
  //     SqlSession 完全包含了面向数据库执行 org.apache.ibatis.jdbc.SQL 命令所需的所有方法。你可以通过 SqlSession 实例来直接执行已映射的 SQL 语句。
      public static SqlSession getSqlSession() {
  //        SqlSession sqlSession = //sqlSessionFactory.openSession();
  //        return sqlSession;
          return sqlSessionFactory.openSession();
      }
  }
  ```

  

## 第四步：

- 创建com.it.pojo包，编写数据库表的关系映射(例如:user表对应user类)

- ```java
  package com.it.pojo;
  
  public class User {
      private int id;
      private String name;
      private String pwd;
  
      public User() {
      }
  
      public User(int id, String name, String pwd) {
          this.id = id;
          this.name = name;
          this.pwd = pwd;
      }
  
      public int getId() {
          return id;
      }
  
      public void setId(int id) {
          this.id = id;
      }
  
      public String getName() {
          return name;
      }
  
      public void setName(String name) {
          this.name = name;
      }
  
      public String getPwd() {
          return pwd;
      }
  
      public void setPwd(String pwd) {
          this.pwd = pwd;
      }
  
      @Override
      public String toString() {
          return "User{" +
                  "id=" + id +
                  ", name='" + name + '\'' +
                  ", pwd='" + pwd + '\'' +
                  '}';
      }
  }
  
  ```

## 第五步：

- 创建com.it.Dao包，编写UserMapper接口(填写增删改查)

- ```java
  package com.it.Dao;
  
  import com.it.pojo.User;
  
  import java.util.List;
  
  public interface UserMapper {
      List<User> getUserList();
  }
  
  ```

## 第六步：

- 在com.it.Dao包，中创建UserMapper.xml配置文件(Dao=Mapper)。(由原来的UserDaoImpl.java实现类 转换 为UserMapper.xml配置文件)

- ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE mapper
          PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  <!--namespace 绑定一对应的Dao/Mapper接口 -->
  <mapper namespace="com.it.Dao.UserMapper">
  
  <!--select查询 id就是UserDao接口里面的方法的名字 resultType/Map是返回一个/多个结果集
  路径就写表的映射的路径-->
   <select id="getUserList" resultType="com.it.pojo.User">
    select * from mybatis.user;
   </select>
  </mapper>
  ```

## 第七步

- 编写测试类

- Junit测试类代码

  ```java
  package com.it.Dao;
  
  //test的路径尽量要跟要测试的类的路径匹配
  
  import com.it.pojo.User;
  import com.it.utils.MybatisUtils;
  import org.apache.ibatis.session.SqlSession;
  import org.junit.Test;
  
  import java.util.List;
  
  public class UserDaoTest {
      @Test
      public void test(){
  //        第一步获取SqlSession对象
          SqlSession sqlSession = MybatisUtils.getSqlSession();
  //        执行sql：方法一
          UserMapper mapper = sqlSession.getMapper(UserMapper.class);
          List<User> userList = mapper.getUserList();
          for (User user : userList) {
              System.out.println(user);
          }
  //        关闭sqlSession
          sqlSession.close();
      }
  }
  
  ```

  

- 错误一:

  org.apache.ibatis.binding.BindingException: Type interface com.it.Dao.UserMapper is not known to the MapperRegistry.

   **<**!--每一个Mapper.xml都需要在mybatis-config.xml核心配置文件中注册-->**
      **<mappers>**
          **<mapper resource="com/it/Dao/UserMapper.xml"/>**
      </mappers>**

- 错误二：

  The error may exist in org/mybatis/example/BlogMapper.xml

  Cause: org.apache.ibatis.builder.BuilderException: Error parsing SQL Mapper Configuration. 

  Cause: java.io.IOException: Could not find resource org/mybatis/example/BlogMapper.xml

  ```xml
     <!--    所有的maven项目都需要加进去这个文件，在父，子pom.xml里都需要加入一下代码   -->   
  <!--在build中配置resources，来防止我们资源导出失败的问题-->
      <build>
          <resources>
  
              <resource>
                  <directory>src/main/resources</directory>
                  <includes>
                      <include>**/*.properties</include>
                      <include>**/*.xml</include>
                  </includes>
                  <filtering>true</filtering>
              </resource>
  
              <resource>
                  <directory>src/main/java</directory>
                  <includes>
                      <include>**/*.properties</include>
                      <include>**/*.xml</include>
                  </includes>
                  <filtering>true</filtering>
              </resource>
  
          </resources>
      </build>
  ```

- 错误三：

- 错误四：**value="com.mysql.cj.jdbc.Driver"  要有 cj** 

- 错误五：时区设置。

  1. 进入命令窗口（Win + R），连接数据库 mysql -hlocalhost -uroot -p，回车，输入密码，回车
  2. 继续输入 show variables like'%time_zone';  （注意不要漏掉后面的分号），显示 SYSTEM 就是没有设置时区啦。
  3. 输入set global time_zone = '+8:00'; 注意不要漏掉后面的分号）





# 2.Mybatis-CRUD

## <u>*增删改must提交事务，模块mybatis-02*</u>

## 1.namespace

- namespace中的名要和UserMapper.xml的接口一致


## 2.Select

- 此时表格映射(pojo)，工具类(utiles)，resources-mybatis-config.xml(配置文件)都已经建立好了。只需要改接口和接口实现.xml和测试类就可以了。

- 带参数的查询：根据id查询用户

  1.接口(UserMapper)：

  ```java
  User getUserById(int id)；
  ```

  2.接口实现.xml(UserMapper.xml)：

  id：接口里的方法名；resultType：返回值类型是User，要写权限路径；parameterType：要传入的参数的类型；where id = #{id}：要传入的参数，书写格式

  ```xml
  <select id="getUserById" resultType="com.it.pojo.User" parameterType="int">
          select * from mybatis.user where id = #{id}
  </select>
  ```

  3.测试：

  第一步和最后一步不变，只改中间即可

  ```java
      @Test
      public void getUserById(){
          //        第一步获取SqlSession对象
          SqlSession sqlSession = MybatisUtils.getSqlSession();
  
          UserMapper mapper = sqlSession.getMapper(UserMapper.class);
          User user = mapper.getUserById(1);
          System.out.println(user);
  
          //        最后一步关闭sqlSession
          sqlSession.close();
  ```

## 3.Insert

1. 接口：

2. ```java
   //    insert一个用户
       int addUser(User user);
   ```

3. 接口实现.xml(UserMapper.xml)：

4. ```xml
   <!--    对象中的属性可以直接取出来 -->
   <insert id="addUser" parameterType="com.it.pojo.User">
       insert into mybatis.user(id,name,pwd) values (#{id},#{name},#{pwd})
   </insert>
   ```

5. 测试：

6. ```java
   @Test
      public void addUser(){
          //        第一步获取SqlSession对象
          SqlSession sqlSession = MybatisUtils.getSqlSession();
     
          UserMapper mapper = sqlSession.getMapper(UserMapper.class);
          int user = mapper.addUser(new User(4, "wc", "444"));
          if (user>0){
              System.out.println("insert success !");
          }
     
          //增删改都需要提交事务 !!!
          sqlSession.commit();
          
          //        最后一步关闭sqlSession
          sqlSession.close();
      }
   ```

## 4.Update

1. 接口：

2. ```java
   //    修改用户
       int updateUser(User user);
   ```

3. 接口实现.xml(UserMapper.xml)：

4. ```xml
   <update id="updateUser" parameterType="com.it.pojo.User">
           update mybatis.user set name = #{name},pwd =  #{pwd} where id = #{id} ;
   </update>
   ```

5. 测试：

6. ```java
   @Test
       public void updateUser(){
           //        第一步获取SqlSession对象
           SqlSession sqlSession = MybatisUtils.getSqlSession();
   
           UserMapper mapper = sqlSession.getMapper(UserMapper.class);
           int user = mapper.updateUser(new User(4, "haha", "123123"));
           if (user>0){
               System.out.println("insert success !");
           }
   
           //增删改都需要提交事务 !!!
           sqlSession.commit();
   
           //        最后一步关闭sqlSession
           sqlSession.close();
       }
   ```

## 5.Delete

1. 接口：

2. ```java
   //    删除用户
       int deleteUser(int id);
   ```

3. 接口实现.xml(UserMapper.xml)：

4. ```xml
   <delete id="deleteUser" parameterType="int">
         delete from mybatis.user where id = #{id}
     </delete>
   ```

5. 测试：

6. ```java
   @Test
       public void deleteUser(){
           //        第一步获取SqlSession对象
           SqlSession sqlSession = MybatisUtils.getSqlSession();
   
           UserMapper mapper = sqlSession.getMapper(UserMapper.class);
           int user = mapper.deleteUser(4);
           if (user>0){
               System.out.println("insert success !");
           }
   
           //增删改都需要提交事务 !!!
           sqlSession.commit();
   
           //        最后一步关闭sqlSession
           sqlSession.close();
       }
   ```

## 6.万能的Map

1. 例子：使用update,只修改pwd

2. 接口：

3. ```java
   //    万能的Map，当数据或者属性很多的时候，可以选择性的单独改变密码或者用户名等等。
       int updateUserByMap(Map<String,Object> map);
   ```

4. 接口实现.xml(UserMapper.xml)：

5. ```xml
   <!--    利用Map进行的update,只修改pwd。如果是User对象的话则所有的属性都需要写入。使用Map可以写你需要修改的-->
       <update id="updateUserByMap" parameterType="map">
           update mybatis.user set pwd = #{userpwd} where id = #{userid} ;
       </update>
   ```

6. 测试：

7. ```java
   @Test
      public void updateUserByMap(){
          //        第一步获取SqlSession对象
          SqlSession sqlSession = MybatisUtils.getSqlSession();
          UserMapper mapper = sqlSession.getMapper(UserMapper.class);
     
          Map<String,Object> map = new HashMap<String,Object>();
          map.put("userid",4);
          map.put("userpwd","789798789798797");
          int user = mapper.updateUserByMap(map);
          if (user>0){
              System.out.println("insert success !");
          }
          //增删改都需要提交事务 !!!
          sqlSession.commit();
     
          //        最后一步关闭sqlSession
          sqlSession.close();
      }
   ```

## 7.模糊查询

1. 例子：使用List<User> 

2. 接口：

3. ```java
   //  模糊查询
       List<User> getUserListByLike(String like);
   ```

4. 接口实现.xml(UserMapper.xml)：

5. ````xml
   <!--    模糊查询-->
       <select id="getUserListByLike" resultType="com.it.pojo.User">
           select * from mybatis.user where name like #{like} ;
       </select>
   ````

6. 测试：

7. ```java
   //    模糊查询
       @Test
       public void getUserListByLike(){
           //        第一步获取SqlSession对象
           SqlSession sqlSession = MybatisUtils.getSqlSession();
           UserMapper mapper = sqlSession.getMapper(UserMapper.class);
           List<User> userListByLike = mapper.getUserListByLike("%w%");
           for (User user : userListByLike) {
               System.out.println(user);
           }
           //        最后一步关闭sqlSession
           sqlSession.close();
       }
   ```



# 3.配置解析

## *<u>模块mybatis-02</u>*

## 1.核心配置文件

- mybatis-config.xml
- MyBatis 的配置文件包含了会深深影响 MyBatis 行为的设置和属性信息。 配置文档的顶层结构如下：
- configuration（配置）
  - [properties（属性）](https://mybatis.org/mybatis-3/zh/configuration.html#properties)
  - [settings（设置）](https://mybatis.org/mybatis-3/zh/configuration.html#settings)
  - [typeAliases（类型别名）](https://mybatis.org/mybatis-3/zh/configuration.html#typeAliases)
  - [typeHandlers（类型处理器）](https://mybatis.org/mybatis-3/zh/configuration.html#typeHandlers)
  - [objectFactory（对象工厂）](https://mybatis.org/mybatis-3/zh/configuration.html#objectFactory)
  - [plugins（插件）](https://mybatis.org/mybatis-3/zh/configuration.html#plugins)
  - [environments](https://mybatis.org/mybatis-3/zh/configuration.html#environments )（环境配置）
    - environment（环境变量）
      - transactionManager（事务管理器）
      - dataSource（数据源）
  - [databaseIdProvider（数据库厂商标识）](https://mybatis.org/mybatis-3/zh/configuration.html#databaseIdProvider)
  - [mappers（映射器）](https://mybatis.org/mybatis-3/zh/configuration.html#mappers)

## 2.environments-环境配置

- MyBatis 可以配置成适应多种环境，**不过要记住：尽管可以配置多个环境，但每个 SqlSessionFactory 实例只能选择一种环境。**
- 可以设置多套运行环境，但只能同时使用一套。Mybatis默认的事务管理是JDBC(当然不止一种事务管理)，连接池:POOLED。

## 3.属性

- 我们可以通过properties属性来实现引用配置文件【db.properties】

## 4.开始改造，模块mybatis-02

### 1.创建db.properties配置文件，.将(db.properties)配置文件引入(mybatis-config.xml)核心配置文件中

- 在resources文件夹中创建新文件：db.properties

- ```properties
  driver=com.mysql.cj.jdbc.Driver
  url=jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8&amp;serverTimezone=Asia/Shanghai
  username=root
  password=3105311
  ```

- 将<properties/>标签写在(mybatis-config.xml)核心配置文件的第一位。并修改核心配置文件中的driver,url...的value。

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE configuration
          PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-config.dtd">
          <!-- configuration核心配置文件 -->
  <configuration>
  
          
  <!--    引入外部(db.properties)配置文件-->
      <properties resource="db.properties"/>
  
      
  <!--    环境 1 -->
      <environments default="development">
          <environment id="development">
              <transactionManager type="JDBC"/>
              <dataSource type="POOLED">
                  
                  
                  <property name="driver" value="${driver}"/>
                  <property name="url" value="${url}"/>
                  <property name="username" value="${username}"/>
                  <property name="password" value="${password}"/>
                  
                  
              </dataSource>
          </environment>
      </environments>
  
  <!--每一个Mapper.xml都需要在mybatis-config.xml核心配置文件中注册-->
      <mappers>
          <mapper resource="com/it/Dao/UserMapper.xml"/>
      </mappers>
  </configuration>
  ```

### 2.typeAliases（类型别名）

- 在" mybatis-config.xml "中编写别名，在 "引入外部(db.properties)配置文件" 的下方编写别名。

- mybatis-config.xml代码：

- ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE configuration
          PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-config.dtd">
  
  <!--    configuration核心配置文件 -->
  <configuration>
  <!--    引入外部(db.properties)配置文件-->
      <properties resource="db.properties"/>
  
      
  <!--    起别名方法一：使用于实体类少的情况。目的，编写接口xml的sql语句时，type写的简单-->
      <typeAliases>
          <typeAlias type="com.it.pojo.User" alias="user"/>
      </typeAliases>
      
  <!--    起别名方法二：使用于实体类多的情况
          意思： com.it.pojo.User的别名默认就是pojo包中User的小写类名。
          在利用方法二的情况下：如果在User类加入注解" @Alias("hellow") ",就能使用注解中的别名
      <typeAliases>
          <package name="com.it.pojo.User"/>
      </typeAliases>
  -->
  
  
  <!--    环境 1 -->
      <environments default="development">
          <environment id="development">
              <transactionManager type="JDBC"/>
              <dataSource type="POOLED">
                  <property name="driver" value="${driver}"/>
                  <property name="url" value="${url}"/>
                  <property name="username" value="${username}"/>
                  <property name="password" value="${password}"/>
              </dataSource>
          </environment>
      </environments>
  
  <!--每一个Mapper.xml都需要在mybatis-config.xml核心配置文件中注册-->
      <mappers>
          <mapper resource="com/it/Dao/UserMapper.xml"/>
      </mappers>
  
  </configuration>
  ```

- 接口实现xml代码：

- ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
          <!DOCTYPE mapper
                  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
                  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
          <!--namespace 绑定一对应的Dao/Mapper接口 -->
          <!--namespace中的名要和UserMapper.xml的接口一致-->
  <mapper namespace="com.it.Dao.UserMapper">
  
      <!--select查询 id就是UserDao接口里面的方法的名字 resultType/Map是返回一个/多个结果集
      路径就写表的映射的路径-->
      <select id="getUserList" resultType="user">
         select * from mybatis.user;
        </select>
  
      <select id="getUserById" resultType="user" parameterType="int">
              select * from mybatis.user where id = #{id}
          </select>
  
      <!--    对象中的属性可以直接取出来 -->
      <insert id="addUser" parameterType="user">
          insert into mybatis.user(id,name,pwd) values (#{id},#{name},#{pwd})
      </insert>
  
      <update id="updateUser" parameterType="user">
          update mybatis.user set name = #{name},pwd = #{pwd} where id = #{id}
      </update>
  
      <delete id="deleteUser" parameterType="int">
          delete from mybatis.user where id = #{id}
      </delete>
  
  
  </mapper>
  ```

### 3.设置（settings）

- 目前需要记住的(都在mybatis文档中)

- ![image-20200209145944601](C:\Users\91566\AppData\Roaming\Typora\typora-user-images\image-20200209145944601.png)

- ![image-20200209145912295](C:\Users\91566\AppData\Roaming\Typora\typora-user-images\image-20200209145912295.png)

- ```xml
  <settings>
    <setting name="cacheEnabled" value="true"/>
    <setting name="lazyLoadingEnabled" value="true"/>
    <setting name="multipleResultSetsEnabled" value="true"/>
    <setting name="useColumnLabel" value="true"/>
    <setting name="useGeneratedKeys" value="false"/>
    <setting name="autoMappingBehavior" value="PARTIAL"/>
    <setting name="autoMappingUnknownColumnBehavior" value="WARNING"/>
    <setting name="defaultExecutorType" value="SIMPLE"/>
    <setting name="defaultStatementTimeout" value="25"/>
    <setting name="defaultFetchSize" value="100"/>
    <setting name="safeRowBoundsEnabled" value="false"/>
    <setting name="mapUnderscoreToCamelCase" value="false"/>
    <setting name="localCacheScope" value="SESSION"/>
    <setting name="jdbcTypeForNull" value="OTHER"/>
    <setting name="lazyLoadTriggerMethods" value="equals,clone,hashCode,toString"/>
  </settings>
  ```

### 4.其他配置

- typeHandlers（类型处理器）
- objectFactory（对象工厂）
- plugins（插件）

### 5.映射器(mappers)

- 采用mybatis文档。
- 设置时，接口和接口.xml的名字要一致。

### 6.生命周期和作用域

- 参考mybatis文件

# 4.ResultMap结果集映射

### **<u>解决属性名和字段名不一致的问题</u>**

## <u>*模块mybatis-03*</u>

- pojo代码：

- ```java
  package com.it.pojo;
  public class User {
      private int id;
      private String name;
  
  //    此时属性名和数据库表中的字段名字不相同，结果就是查不出pwd的结果
  //    原因：sql=select name，id，pwd from user。
  //    数据库表中名为pwd而映射表中是password
      
      private String password;
  
      public User() {
      }
  
      public User(int id, String name, String password) {
          this.id = id;
          this.name = name;
          this.password = password;
      }
  
      public int getId() {
          return id;
      }
  
      public void setId(int id) {
          this.id = id;
      }
  
      public String getName() {
          return name;
      }
  
      public void setName(String name) {
          this.name = name;
      }
  
      public String getpassword() {
          return password;
      }
  
      public void setpsaaword(String password) {
          this.password = password;
      }
      @Override
      public String toString() {
          return "User{" +
                  "id=" + id +
                  ", name='" + name + '\'' +
                  ", pwd='" + password + '\'' +
                  '}';
      }
  }
  ```

- 接口实现.xml代码：

- ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
              <!DOCTYPE mapper
                      PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
                      "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
      <mapper namespace="com.it.Dao.UserMapper">
  
          
  <!--    id为你添加的resultMap标签的名字。。type为要映射的类的名字-->
      <resultMap id="UserMap" type="User">
  <!--    column是列的意思，就是对应数据库表中的列的名字，
          property是属性的意思，也就是对应类的属性名字。
  -->
          <result column="pwd" property="password"/>
          <result column="id" property="id"/>
          <result column="name" property="name"/>
      </resultMap>
  
          
  <!--添加一个resultMap标签-->
      <select id="getUserById" resultMap="UserMap">
              select * from mybatis.user where id = #{id}
      </select>
  
  
      </mapper>
  ```



# 5.日志

## 1.日志工厂

## ***<u>模块mybatis-04</u>***

- 如果一个数据库的操作，出现了异常，我妈需要排错，那么日志就是最好的帮手。
- 根据mybatis文档setting(设置)![image-20200210131801297](C:\Users\91566\AppData\Roaming\Typora\typora-user-images\image-20200210131801297.png)
-  LOG4J (掌握)
- STDOUT_LOGGING(掌握)(默认的标准的日志工厂)

## 2.开始编写

### 使用STDOUT_LOGGING默认日志工厂

- 写在"引入外部配置文件"的下面。

- mybatis-config.xml代码：

- ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE configuration
          PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-config.dtd">
  
  <!--    configuration核心配置文件 -->
  <configuration>
  
  <!--    引入外部(db.properties)配置文件-->
      <properties resource="db.properties"/>
  
  <!--    使用标准的默认的日志工厂-->
      <settings>
          <setting name="logImpl" value="STDOUT_LOGGING"/>
      </settings>
      
  
  <!--    起别名方法一：使用于实体类少的情况-->
      <typeAliases>
          <typeAlias type="com.it.pojo.User" alias="user"/>
      </typeAliases>
  
  <!--    起别名方法二：使用于实体类多的情况
          意思： com.it.pojo.User的别名默认就是pojo包中User的类名。
          在利用方法二的情况下：如果在User类加入注解" @Alias("hellow") ",就能使用注解中的别名
      <typeAliases>
          <package name="com.it.pojo.User"/>
      </typeAliases>
  -->
  
  
  <!--    环境 1 -->
      <environments default="development">
          <environment id="development">
              <transactionManager type="JDBC"/>
              <dataSource type="POOLED">
                  <property name="driver" value="${driver}"/>
                  <property name="url" value="${url}"/>
                  <property name="username" value="${username}"/>
                  <property name="password" value="${password}"/>
              </dataSource>
          </environment>
      </environments>
  
  <!--每一个Mapper.xml都需要在mybatis-config.xml核心配置文件中注册-->
      <mappers>
          <mapper resource="com/it/Dao/UserMapper.xml"/>
      </mappers>
  
  </configuration>
  ```

- 运行过后，看关键部分为：![image-20200210140831218](C:\Users\91566\AppData\Roaming\Typora\typora-user-images\image-20200210140831218.png)

### 使用LOG4J日志工厂

- 导入LOG4J的jar：

  ```xml
  <!-- https://mvnrepository.com/artifact/log4j/log4j -->
  <dependency>
      <groupId>log4j</groupId>
      <artifactId>log4j</artifactId>
      <version>1.2.17</version>
  </dependency>
  ```

- 在resource文件夹中创建log4j.properties文件，内容：

  ```properties
  #将等级为DEBUG的日志信息输出到console和file这两个目的地，console和file的定义在下面的代码
  log4j.rootLogger=DEBUG,console,file
  
  #控制台输出的相关设置
  log4j.appender.console = org.apache.log4j.ConsoleAppender
  log4j.appender.console.Target = System.out
  log4j.appender.console.Threshold=DEBUG
  log4j.appender.console.layout = org.apache.log4j.PatternLayout
  log4j.appender.console.layout.ConversionPattern=[%c]-%m%n
  
  #文件输出的相关设置
  log4j.appender.file = org.apache.log4j.RollingFileAppender
  log4j.appender.file.File=./log/kuang.log
  log4j.appender.file.MaxFileSize=10mb
  log4j.appender.file.Threshold=DEBUG
  log4j.appender.file.layout=org.apache.log4j.PatternLayout
  log4j.appender.file.layout.ConversionPattern=[%p][%d{yy-MM-dd}][%c]%m%n
  
  #日志输出级别
  log4j.logger.org.mybatis=DEBUG
  log4j.logger.java.sql=DEBUG
  log4j.logger.java.sql.Statement=DEBUG
  log4j.logger.java.sql.ResultSet=DEBUG
  log4j.logger.java.sql.PreparedStatement=DEBUG
  ```

- 在mybatis-config.xml中加入配置，代码：

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE configuration
          PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-config.dtd">
  
  <!--    configuration核心配置文件 -->
  <configuration>
  
  <!--    引入外部(db.properties)配置文件-->
      <properties resource="db.properties"/>
  
  
  <!--    使用STDOUT_LOGGING标准的默认的日志工厂
      <settings>
          <setting name="logImpl" value="STDOUT_LOGGING"/>
      </settings>
  -->
  <!--    使用log4j日志工厂-->
      <settings>
          <setting name="logImpl" value="LOG4J"/>
      </settings>
  
  
  <!--    起别名方法一：使用于实体类少的情况-->
      <typeAliases>
          <typeAlias type="com.it.pojo.User" alias="user"/>
      </typeAliases>
  
  <!--    起别名方法二：使用于实体类多的情况
          意思： com.it.pojo.User的别名默认就是pojo包中User的类名。
          在利用方法二的情况下：如果在User类加入注解" @Alias("hellow") ",就能使用注解中的别名
      <typeAliases>
          <package name="com.it.pojo.User"/>
      </typeAliases>
  -->
  
  
  <!--    环境 1 -->
      <environments default="development">
          <environment id="development">
              <transactionManager type="JDBC"/>
              <dataSource type="POOLED">
                  <property name="driver" value="${driver}"/>
                  <property name="url" value="${url}"/>
                  <property name="username" value="${username}"/>
                  <property name="password" value="${password}"/>
              </dataSource>
          </environment>
      </environments>
  
  <!--每一个Mapper.xml都需要在mybatis-config.xml核心配置文件中注册-->
      <mappers>
          <mapper resource="com/it/Dao/UserMapper.xml"/>
      </mappers>
  
  </configuration>
  ```

  

  **简单使用**

- 在测试类代码：

  ```java
  package Dao;
  
  //test的路径尽量要跟要测试的类的路径匹配
  import com.it.Dao.UserMapper;
  import com.it.pojo.User;
  import com.it.utils.MybatisUtils;
  import org.apache.ibatis.session.SqlSession;
  import org.junit.Test;
  import org.apache.log4j.Logger;
  
  
  public class UserDaoTest {
  
  //    log4j，获取类字节码
      static Logger logger = Logger.getLogger(UserDaoTest.class);
  
      @Test
      public void getUserById(){
          //        第一步获取SqlSession对象
          SqlSession sqlSession = MybatisUtils.getSqlSession();
  
          UserMapper mapper = sqlSession.getMapper(UserMapper.class);
          User user = mapper.getUserById(1);
          System.out.println(user);
  
          //        最后一步关闭sqlSession
          sqlSession.close();
  
      }
  
      @Test
      public void testLog4j(){
          //日志级别
      logger.info("info进入了：testLog4j");
      logger.debug("debug进入了：testLog4j");
      logger.error("error进入了：testLog4j");
      }
  }
  
  ```

- 在当前文件目录下生成一个log日志文件，运行testLog4j()方法时，将结果存在在log日志文件中

  ![image-20200210153608684](C:\Users\91566\AppData\Roaming\Typora\typora-user-images\image-20200210153608684.png)



# 6.分页

## ***<u>模块mybatis-04</u>***

## Limit分页

- 接口代码：

  ```java
  //    分页
      List<User> getUserByLimit(Map<String,Integer> map);
  ```

- 接口实现类.xml代码：

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
              <!DOCTYPE mapper
                      PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
                      "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
              <!--namespace 绑定一对应的Dao/Mapper接口 -->
              <!--namespace中的名要和UserMapper.xml的接口一致-->
      <mapper namespace="com.it.Dao.UserMapper">
  
  <!--    id为你添加的resultMap标签的名字。。type为要映射的类的名字-->
      <resultMap id="UserMap" type="User">
  <!--    column是列的意思，就是对应数据库表中的列的名字，
          property是属性的意思，也就是对应类的属性名字。
  -->
          <result column="pwd" property="password"/>
          <result column="id" property="id"/>
          <result column="name" property="name"/>
      </resultMap>
  
  
  <!--添加一个resultMap标签-->
      <select id="getUserById" resultMap="UserMap">
              select * from mybatis.user where id = #{id}
      </select>
  <!--    分页; 使用参数为Map时，parameterType一定要写，Map的别名是'map'，int的别名是 '_int' -->
      <select id="getUserByLimit" resultMap="UserMap" parameterType="map">
              select * from mybatis.user limit #{startIndex},#{pageSize}
      </select>
  
      </mapper>
  ```

- 测试代码：

  ```java
  package Dao;
  
  //test的路径尽量要跟要测试的类的路径匹配
  import com.it.Dao.UserMapper;
  import com.it.pojo.User;
  import com.it.utils.MybatisUtils;
  import org.apache.ibatis.session.SqlSession;
  import org.junit.Test;
  import org.apache.log4j.Logger;
  
  import java.io.LineNumberReader;
  import java.util.HashMap;
  import java.util.List;
  import java.util.Map;
  
  
  public class UserDaoTest {
  
  //    log4j，获取类字节码
      static Logger logger = Logger.getLogger(UserDaoTest.class);
  
      @Test
      public void getUserById(){
          //        第一步获取SqlSession对象
          SqlSession sqlSession = MybatisUtils.getSqlSession();
  
          UserMapper mapper = sqlSession.getMapper(UserMapper.class);
          User user = mapper.getUserById(1);
          System.out.println(user);
  
          //        最后一步关闭sqlSession
          sqlSession.close();
  
      }
      @Test
      public void testLog4j(){
  //        日志级别
      logger.info("info进入了：testLog4j");
      logger.debug("debug进入了：testLog4j");
      logger.error("error进入了：testLog4j");
      }
      @Test
      public void getUserByLimit(){
          SqlSession sqlSession = MybatisUtils.getSqlSession();
          UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
  
          Map<String, Integer> map = new HashMap<String, Integer>();
          map.put("startIndex",1);
          map.put("pageSize",3);
  
          List<User> userList = userMapper.getUserByLimit(map);
          for (User user : userList) {
              System.out.println(user);
          }
          sqlSession.close();
      }
  }
  
  ```

## RowBounds分页(不推荐)

## 分页插件

# 7.使用注解开发

## *<u>模块mybatis-05</u>*

## 在MybatisUtils.java中使用自动提交事务。只能适用于简单的sql，不能多表联查。

- utils包中MybatisUtils.java代码：

  ```java
  public static SqlSession getSqlSession() {
  //        SqlSession sqlSession = sqlSessionFactory.openSession();
  //        return sqlSession;
  
  //        设置增删改查自动提交事务，true。
          return sqlSessionFactory.openSession(true);
      }
  ```

## 增删改查注解

- 只需修改接口和测试类即可。

- mybatis-config.xml中

  ```xml
  <!--每一个Mapper.xml都需要在mybatis-config.xml核心配置文件中注册
  注解就用class。class的路径和resource的路径不同，class(.不加.xml)，否则就写resource(/加.xml)。
  Mapper.xml和接口在同一包下且必须同名，用clss。
  -->
      <mappers>
          <mapper class="com.it.Dao.UserMapper"/>
      </mappers>
  ```

- 接口UserMapper代码：

  ```java
  package com.it.Dao;
  import com.it.pojo.User;
  import org.apache.ibatis.annotations.*;
  import java.util.List;
  import java.util.Map;
  public interface UserMapper {
  //    查询全部用户
      @Select("select * from user")
      List<User> getUserList();
      
  //    存在多个参数
      @Select("select * from user where id = #{id} and pwd = #{pwd}")
      User getUserByIdPwd(@Param("id")int id,@Param("pwd") String pwd);
      
  //    增:返回值类型只能用int不能用User，否则会报错org.apache.ibatis.binding.BindingException: Mapper method 'com.it.Dao.UserMapper.addUser' has an unsupported return type: class com.it.pojo.User
      @Insert("insert into user(id,name,pwd) values (#{id},#{name},#{pwd})")
      int addUser(User user);
      
  //  改 @Param,只要有基本类型参数，有几个参数就写几个@Param。
      @Update("update user set name=#{name} where id=#{id}")
      int updateUser(@Param("name") String name,@Param("id") int id);
  
  //    删
      @Delete("delete from user where id = #{id}")
      int deleteUser(@Param("id") int id);
  }
  ```

- 测试代码：

  ```java
  package Dao;
  
  //test的路径尽量要跟要测试的类的路径匹配
  
  import com.it.Dao.UserMapper;
  import com.it.pojo.User;
  import com.it.utils.MybatisUtils;
  import org.apache.ibatis.session.SqlSession;
  import org.junit.Test;
  
  import java.util.HashMap;
  import java.util.List;
  import java.util.Map;
  
  public class UserDaoTest {
      @Test
      public void getUserList(){
          SqlSession sqlSession = MybatisUtils.getSqlSession();
              UserMapper mapper = sqlSession.getMapper(UserMapper.class);
              List<User> userList = mapper.getUserList();
              sqlSession.close();
      }
      @Test
      public void getUserByIdPwd(){
          SqlSession sqlSession = MybatisUtils.getSqlSession();
          UserMapper mapper = sqlSession.getMapper(UserMapper.class);
          User userByIdPwd = mapper.getUserByIdPwd(1,"111");
          System.out.println(userByIdPwd);
          sqlSession.close();
      }
      @Test
      public void addUser(){
          SqlSession sqlSession = MybatisUtils.getSqlSession();
          UserMapper mapper = sqlSession.getMapper(UserMapper.class);
          mapper.addUser(new User(7,"c5","253"));
          sqlSession.close();
      }
      @Test
      public void updateUser(){
          SqlSession sqlSession = MybatisUtils.getSqlSession();
          UserMapper mapper = sqlSession.getMapper(UserMapper.class);
          mapper.updateUser("qqq",1);
          sqlSession.close();
      }
      @Test
      public void deleteUser(){
          SqlSession sqlSession = MybatisUtils.getSqlSession();
          UserMapper mapper = sqlSession.getMapper(UserMapper.class);
          mapper.deleteUser(7);
          sqlSession.close();
      }
  }
  ```


## IDEA中的Lombok

- IDEA中：File→Settings→Plugins→搜索Lombok

- 引入Lombok的maven依赖。

  ```xml
  <dependencies>
  
          <dependency>
              <groupId>org.projectlombok</groupId>
              <artifactId>lombok</artifactId>
              <version>1.18.8</version>
          </dependency>
  
      </dependencies>
  ```

  

- @注解加载加载单独的一个变量上时，只生成这一个变量的方法

- ```java
  @Getter@Setter
  @FieldNameConstants
  @ToString
  @EqualsAndHashCode
  @AllArgsConstructor(所有参数构造)
  @NoArgsConstructor(无参构造)
  @Data(相当于添加了@Getter@Setter@ToString@NoArgsConstructor(无参构造))
  ```

- pojo包数据库表的映射类-User.java代码：

  ```java
  package com.it.pojo;
  
  import lombok.AllArgsConstructor;
  import lombok.Data;
  
  @Data
  @AllArgsConstructor
  public class User {
      private int id;
      private String name;
      private String pwd;
  }
  
  ```

# 8.复杂查询环境搭建

## *<u>模块mybatis-06</u>*

- 添加2张新表和内容

- ```mysql
  CREATE TABLE `student` (
    `id` int(10) NOT NULL,
    `name` varchar(30) DEFAULT NULL,
    `tid` int(10) DEFAULT NULL,
    PRIMARY KEY (`id`),
    KEY `ftkid` (`tid`),
    CONSTRAINT `ftkid` FOREIGN KEY (`tid`) REFERENCES `teacher` (`id`)
  ) ENGINE=InnoDB DEFAULT CHARSET=utf8;
  
  CREATE TABLE `teacher` (
    `id` int(10) NOT NULL,
    `name` varchar(30) DEFAULT NULL,
    PRIMARY KEY (`id`)
  ) ENGINE=InnoDB DEFAULT CHARSET=utf8;
  ```

- 结构如下：

- ![image-20200213152632620](C:\Users\91566\AppData\Roaming\Typora\typora-user-images\image-20200213152632620.png)

- 注：接口.xml文件放在了resource资源文件中，注意包结构是使用可以展开的包结构。使用Lombok注解。

- src.Dao中数据为空，src.pojo中为空，resource.Dao中为空，Test为空，utils不变。

- mybatis-config.xml代码为：

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE configuration
          PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-config.dtd">
  
  <!--    configuration核心配置文件 -->
  <configuration>
  
      <!--    引入外部(db.properties)配置文件-->
      <properties resource="db.properties"/>
  
  
      <!--    使用STDOUT_LOGGING标准的默认的日志工厂    -->
          <settings>
              <setting name="logImpl" value="STDOUT_LOGGING"/>
          </settings>
  
      <!--    使用log4j日志工厂(需要maven导入依赖)
      <settings>
          <setting name="logImpl" value="LOG4J"/>
      </settings>
  -->
  
  
      <!--    起别名方法一：使用于实体类少的情况。目的，编写接口xml的sql语句时，type写的简单-->
      <typeAliases>
          <typeAlias type="com.it.pojo.Teacher" alias="Teacher"/>
          <typeAlias type="com.it.pojo.Student" alias="Student"/>
      </typeAliases>
  
      <!--    起别名方法二：使用于实体类多的情况
              意思： com.it.pojo.User的别名默认就是pojo包中User的类名。
              在利用方法二的情况下：如果在User类加入注解" @Alias("hellow") ",就能使用注解中的别名
          <typeAliases>
              <package name="com.it.pojo.User"/>
          </typeAliases>
      -->
  
  
      <!--    环境 1 -->
      <environments default="development">
          <environment id="development">
              <transactionManager type="JDBC"/>
              <dataSource type="POOLED">
                  <property name="driver" value="${driver}"/>
                  <property name="url" value="${url}"/>
                  <property name="username" value="${username}"/>
                  <property name="password" value="${password}"/>
              </dataSource>
          </environment>
      </environments>
  
      <!--每一个Mapper.xml都需要在mybatis-config.xml核心配置文件中注册-->
  <!--    读入2个接口路径-->
      <mappers>
          <mapper class="com.it.Dao.StudentMapper"/>
          <mapper class="com.it.Dao.TeacherMapper"/>
      </mappers>
  
  </configuration>
  ```

# 9.多对一

## *<u>模块mybatis-06</u>*

## 通过多个学生查询一个老师，通过学生查询老师的信息

## 方法一(按查询嵌套处理)

- Dao中TeacherMapper接口为空，StudentMapper代码为：

  ```java
  package com.it.Dao;
  import com.it.pojo.Student;
  import java.util.List;
  public interface StudentMapper {
  
  //    多表联查，查询学生的所有信息以及老师的信息
      List<Student> getStudent();
  }
  
  ```

- pojo中Teacher为空，Student代码为：

  ```java
  package com.it.pojo;
  import lombok.Data;
  @Data
  public class Student {
      private int id;
      private String name;
  //通过多个学生查老师。此处使用老师类型作为参数，意为返回一个老师对象。
      private Teacher tid;
  }
  
  ```

- resources.com.it.Dao.TeacherMapper.xml为空，resources.com.it.Dao.StudentMapper.xml代码为：

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE mapper
          PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  <!--namespace 绑定一对应的Dao/Mapper接口 -->
  <!--namespace中的名要和UserMapper.xml的接口一致-->
  <mapper namespace="com.it.Dao.StudentMapper">
  
  <!--    1.查出学生信息 2.通过学生的tid查询到老师的信息-->
  
  <!--    第一个id为方法名-->
      <select id="getStudent" resultMap="student→teacher">
          select * from student
      </select>
  
  <!--    id为select的resultMap的值，type是映射的类是名字-Student，，相当于select和resulMap连接了起来-->
      <resultMap id="student→teacher" type="Student">
          <result property="id" column="id"/>
          <result property="name" column="name"/>
  <!--    在Student.java中第三个变量类型是Teacher类型，是复杂类型，处理：将result换成，
          若是对象：association，若是集合：collection
          property对应javaType，column对应select
          javaType：给property一个类型
          select：表明colum要干什么,要select="getTeacher"
          select：接着连接下一个id="getTeacher"-->
          <association property="tid" column="tid" javaType="Teacher" select="getTeacher"/>
      </resultMap>
  
  <!--此时TeacherMapper接口没方法，那就设一个getTeacher方法-->
      <select id="getTeacher" resultType="Teacher">
          select * from teacher where id = #{tid}
      </select>
  
  
  
  </mapper>
  ```

## 方法二(按结果嵌套处理)

- 其他与方法一相同resources.com.it.Dao.TeacherMapper.xml为空，修改resources.com.it.Dao.StudentMapper.xml代码为：

  ```xml
  <!--方法二：直接写出完整sql。
      然后写出resultMap，此时第三个属性仍是复杂类型。
      在association只写property对应javaType="Teacher"
      然后我们查询老师的id和name，然后在association里面再编写Teacher的property和columu-->
      <select id="getStudent2" resultMap="student→teacher2">
          select s.id sid,s.name,sname,t.name tname,t.id tid from student s,teacher t where s.tid = t.id
      </select>
  
      <resultMap id="student→teacher2" type="Student">
  <!-- 注意：查询的就是一个表。此处的column(数据库中的列)，就变成了s.id的别名sid等-->
          <result property="id" column="sid"/>
          <result property="name" column="sname"/>
          <association property="tid" javaType="Teacher">
              <result property="name" column="tname"/>
              <result property="id" column="tid"/>
          </association>
      </resultMap>
  ```

# 10.一对多

## *<u>模块mybatis-07</u>*

## 通过一个老师查询多个学生，通过老师查询学生的信息

- 接口StudentMapper为空，TeacherMapper代码：

  ```java
  package com.it.Dao;
  import com.it.pojo.Teacher;
  import org.apache.ibatis.annotations.Param;
  public interface TeacherMapper {
  //    获取老师的信息以及老师下的所有学生
      Teacher getTeacher(@Param("id")int id);
  }
  ```

- pojo.Student代码：

  ```java
  package com.it.pojo;
  
  import lombok.Data;
  
  @Data
  public class Student {
      private int id;
      private String name;
      private int tid;
  }
  
  ```

- pojo.Teacher代码：

  ```java
  package com.it.pojo;
  import lombok.Data;
  import java.util.List;
  @Data
  public class Teacher {
      private int id;
      private String name;
  //    一个老师拥有多个学生
      private List<Student> students;
  }
  ```

- 其他与方法一相同resources.com.it.Dao.StudentMapper.xml为空，修改resources.com.it.Dao.TeacherMapper.xml代码为：

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE mapper
          PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  <!--namespace 绑定一对应的Dao/Mapper接口 -->
  <!--namespace中的名要和UserMapper.xml的接口一致-->
  <mapper namespace="com.it.Dao.TeacherMapper">
  
      <select id="getTeacher" resultMap="teacher→student">
          select s.name sname,s.id sid,t.name tname,t.id tid from teacher t,student s where s.tid = t.id and t.id = #{id}
      </select>
  
      <resultMap id="teacher→student" type="Teacher">
          <result property="id" column="tid"/>
          <result property="name" column="tname"/>
  <!--        集合中泛型，使用ofType获取。如果的对象的时候用javaType给定类型-->
      <collection property="students" ofType="Student">
          <result property="id" column="sid"/>
          <result property="name" column="sname"/>
          <result property="tid" column="sid"/>
      </collection>
  </resultMap>
  </mapper>
  ```

# 11.动态SQL

## *<u>模块mybatis-08</u>*(开启自动提交事务)

## 小拓展

- 在com.it.utils中加入IDUtils类，作为可以生成随机id的类，代码：

  ```java
  package com.it.utils;
  import org.junit.jupiter.api.Test;
  import java.util.UUID;
  public class IDUtils {
      public static String getID(){
          return UUID.randomUUID().toString().replaceAll("-","...");
      }
      @Test
      public void test(){
          System.out.println(IDUtils.getID());
      }
  }
  ```

- | mapUnderscoreToCamelCase | 是否开启自动驼峰命名规则（camel case）映射，即从经典数据库列名 A_COLUMN 到经典 Java 属性名 aColumn 的类似映射。 |
  | ------------------------ | ------------------------------------------------------------ |
  | 代码:                    | <!--  使用STDOUT_LOGGING标准的默认的日志工厂-->     <settings>         <setting name="logImpl" value="STDOUT_LOGGING"/>         <setting name="mapUnderscoreToCamelCase" value="true"/>     </settings> |

## 开始改造：(Where-IF，Where-choose-when-otherwise，Set-IF)

- 接口代码：

  ```java
  package com.it.Dao;
  import com.it.pojo.Blog;
  import org.apache.ibatis.annotations.Insert;
  import java.util.List;
  import java.util.Map;
  public interface BlogMapper {
  //    插入语句
      int insertBlog(Blog blog);
  
  //    Where-IF语句，查询blog。动态的根据插入的不同参数，进行不同的查询
      List<Blog> queryBlogIF(Map map);
  
  //    Where-choose-when-otherwise语句，查询blog。类似switch语句
      List<Blog> queryBlogChoose(Map map);
  
  //    set-if语句更新blog
      int updateBlog(Map map);
  
  }
  
  ```

- 接口.xml代码：

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE mapper
          PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  <!--namespace 绑定一对应的Dao/Mapper接口 -->
  <!--namespace中的名要和UserMapper.xml的接口一致-->
  <mapper namespace="com.it.Dao.BlogMapper">
  <!--插入数据-->
      <insert id="insertBlog" parameterType="blog">
          insert into mybatis.blog(id,title,author,create_time,views) values (#{id},#{title},#{author},#{createTime},#{views})
      </insert>
  <!--Where-IF语句。有了where标签，当只满足title时，语句前面的and会被忽略-->
      <select id="queryBlogIF" parameterType="map" resultType="blog">
              select * from blog
              <where>
              <if test="id != null">
              --如果id ！=null，就追加一个and id=#{id}
                  id = #{id}
              </if>
              <if test="title != null">
                  and title = #{title}
              </if>
              <if test="author != null">
                  and author = #{author}
              </if>
              <if test="create_time != null">
                  and create_time = #{create_time}
              </if>
              <if test="views != null">
                  and views = #{views}
              </if>
              </where>
          </select>
  <!--Where-choose-when-otherwise语句。当传入的参数都不在when中存在时，就执行otherwise-->
      <select id="queryBlogChoose" parameterType="map" resultType="blog">
              select * from blog
          <where>
              <choose>
                  <when test="id != null">id = #{id}</when>
                  <when test="title != null">and title = #{title}</when>
                  <when test="author != null">and author = #{author}</when>
                  <when test="create_time != null">and create_time = #{create_time}</when>
                  <when test="views != null">and views = #{views}</when>
                  <otherwise>
                      1=1
                  </otherwise>
              </choose>
          </where>
      </select>
  <!--set-if语句更新Blog-->
      <update id="updateBlog" parameterType="map">
          update mybatis.blog
          <set>
              <if test="title != null">title = #{title},</if>
              <if test="author != null">author = #{author},</if>
              <if test="create_time != null">create_time = #{create_time},</if>
              <if test="views != null">views = #{views}</if>
          </set>
          where id = #{id}
      </update>
  </mapper>
  ```

- 测试代码：

  ```java
  package Dao;
  
  import com.it.Dao.BlogMapper;
  import com.it.pojo.Blog;
  import com.it.utils.IDUtils;
  import com.it.utils.MybatisUtils;
  import org.apache.ibatis.session.SqlSession;
  import org.junit.Test;
  
  import java.util.Date;
  import java.util.HashMap;
  import java.util.List;
  import java.util.Map;
  
  public class MyTest {
      @Test //插入语句
     public void insert(){
          SqlSession sqlSession = MybatisUtils.getSqlSession();
          BlogMapper mapper = sqlSession.getMapper(BlogMapper.class);
  
          Blog blog = new Blog();
  
          blog.setId(IDUtils.getID());
          blog.setTitle("Mybatis so easy");
          blog.setAuthor("Mr.1");
          blog.setCreateTime(new Date());
          blog.setViews(999);
          mapper.insertBlog(blog);
  
          blog.setId(IDUtils.getID());
          blog.setTitle("Spring so easy");
          mapper.insertBlog(blog);
  
          blog.setId(IDUtils.getID());
          blog.setTitle("Math so easy");
          mapper.insertBlog(blog);
  
          blog.setId(IDUtils.getID());
          blog.setTitle("Mysql so easy");
          mapper.insertBlog(blog);
  
          sqlSession.close();
      }
      @Test//Where-IF语句，查询blog。动态的根据插入的不同参数，进行不同的查询。可以同时满足多个条件
       public void queryBlogIF(){
           SqlSession sqlSession = MybatisUtils.getSqlSession();
           BlogMapper mapper = sqlSession.getMapper(BlogMapper.class);
  
           Map map = new HashMap();
           map.put("title","Mybatis so easy");
           map.put("views","888");
  
           List<Blog> blogs = mapper.queryBlogIF(map);
           for (Blog blog : blogs) {
                System.out.println(blog);
           }
           sqlSession.close();
      }
      @Test//Where-choose-when-otherwise语句，查询blog。类似switch语句。只能满足最先的那个条件。
      public void queryBlogChoose(){
          SqlSession sqlSession = MybatisUtils.getSqlSession();
          BlogMapper mapper = sqlSession.getMapper(BlogMapper.class);
  
          Map map = new HashMap();
          map.put("title","Mybatis so easy");
  
          mapper.queryBlogChoose(map);
          System.out.println(map);
          sqlSession.close();
      }
      @Test//    set-if语句更新Blog
      public void updateBlog(){
          SqlSession sqlSession = MybatisUtils.getSqlSession();
          BlogMapper mapper = sqlSession.getMapper(BlogMapper.class);
  
          Map map = new HashMap();
          map.put("id","92036d82.bd3b.4a78.b602.9d3c5ec71a46");
          map.put("views",777);
          mapper.updateBlog(map);
          sqlSession.close();
      }
  }
  
  ```

## SQL片段

- 接口.xml代码:

  ```java
  <!--将每次都要编写的IF语句提取出来，id随便取-->
  <sql id = "sql-if">
  <if test="title != null">title = #{title},</if>
  <if test="author != null">author = #{author},</if>
  <if test="create_time != null">create_time = #{create_time},</if>
  <if test="views != null">views = #{views}</if>
  </sql>
      
  <select id="queryBlogIF" parameterType="map" resultType="blog">
     select * from blog
     <where>
  <!--引用提取出来的SQL片段SQL-->
         <include refid="sql-if"/>     
      </where>
  </select>
  ```

## For each

- 接口.xml代码:

  ```xml
  <!--查询where 1=1 and (id=0 or id=2 or id=3)用foreach-->
  <select id="queryBlogForEach" parameterType="map" resultType="blog">
      select * from blog
      <where>
  <!--类似集合的foreach循环。foreach标签内容分别为：给定一个集合的名字，将取出的内容给定一个名字，开始是的样子and (,结尾的样子)，分开分隔是or。id和#{id}对应-->
          <foreach clloection="list" item="id" open="and (" close=")" separator="or"> id = #{id} </foreach>
      </where>
  ```

- 测试代码(主要部分)：

  ```java
  //通过id查询。将List集合存到Map里面，List里面都是id
  Map map = new HashMap();
  List<Integer> list = new ArraryList<Integer>();
  list.add(1);
  map.put(list);
  mapper.queryBlogForEach(map);
  ```

# 12.缓存

## *<u>模块mybatis-09</u>*

## 开启自动提交日志

## 一级缓存

- 一级缓存默认开启，只在一次sqlSession中有效，也就是从getSqlSession()到close()的过程

- 两次查询只执行了一次sql语句，次sql查询被加入到缓存当中。
- ![image-20200215005441929](C:\Users\91566\AppData\Roaming\Typora\typora-user-images\image-20200215005441929.png)

- 缓存失效的情况：

  | 查询不同的东西，如不同的id                                   |
  | ------------------------------------------------------------ |
  | 增删改语句就会可能改变sql的内容。因此，执行完增删改之后，就会重新执行sql，刷新缓存。 |
  | 查询不同的Mapper.xml                                         |
  | 手动清理缓存：sqlSession.clearCache();                       |

## 二级缓存

### 步骤：

- 开启全局缓存，默认是开启的，但是要主动声明一下。在mybatis文档是(setting配置)中。

  | cacheEnabled | 全局地开启或关闭配置文件中的所有映射器已经配置的任何缓存。 |
  | ------------ | ---------------------------------------------------------- |
  | 默认         | true                                                       |

- pojo.User.java加@AllArgsConstructor注解并实现序列化：

  ```java
  package com.it.pojo;
  import lombok.AllArgsConstructor;
  import lombok.Data;
  import lombok.NoArgsConstructor;
  import java.io.Serializable;
  @Data
  @AllArgsConstructor
  @NoArgsConstructor
  public class User implements Serializable {
      private int id;
      private String name;
      private String pwd;
  }
  ```

- 在UserMapper.xml中配置缓存，代码：

  ```java
  <?xml version="1.0" encoding="UTF-8" ?>
              <!DOCTYPE mapper
                      PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
                      "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
              <!--namespace 绑定一对应的Dao/Mapper接口 -->
              <!--namespace中的名要和UserMapper.xml的接口一致-->
      <mapper namespace="com.it.Dao.UserMapper">
  
      
  <!--    配置二级缓存。以IO流为格式，每隔60s缓存一次，最多缓存512个，是否只读，(此些配置完全可以不写)，pojo加@AllArgsConstructor注解并实现序列化-->
      <cache
              eviction="FIFO"
              flushInterval="60000"
              size="512"
              readOnly="true"/>
  
  
  <!--  useCache=false表示这条语句不进入缓存，true是进入缓存  -->
      <select id="quaryUserById" resultType="user" useCache="true">
          select * from mybatis.user where id = #{id}
      </select>
  
      </mapper>
  ```

### 查询顺序：

- 先找二级缓存，再找一级缓存，如果都没有，就去连接数据库

## 自定义缓存(Ehcache)

### 步骤：

- 导入依赖：

  ```java
  <!-- https://mvnrepository.com/artifact/org.mybatis.caches/mybatis-ehcache -->
  <dependency>
      <groupId>org.mybatis.caches</groupId>
      <artifactId>mybatis-ehcache</artifactId>
      <version>1.1.0</version>
  </dependency>
  ```

- 在接口实现Mapper.xml中：

  ```xml
  <cache type="org.mybatis.caches.encache.EhcacheCache"/>
  ```

- 在resource中创建ehcache.xml文件：

  ```xml
  
  <?xml version="1.0" encoding="UTF-8"?>
  <ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:noNamespaceSchemaLocation="http://ehcache.org/ehcache.xsd"
           updateCheck="false">
      
      <defaultCache
              eternal="false"
              maxElementsInMemory="10000"
              overflowToDisk="false"
              diskPersistent="false"
              timeToIdleSeconds="1800"
              timeToLiveSeconds="259200"
              memoryStoreEvictionPolicy="LRU"/>
   
      <cache
              name="cloud_user"
              eternal="false"
              maxElementsInMemory="5000"
              overflowToDisk="false"
              diskPersistent="false"
              timeToIdleSeconds="1800"
              timeToLiveSeconds="1800"
              memoryStoreEvictionPolicy="LRU"/>
      
      <!--
         diskStore：为缓存路径，ehcache分为内存和磁盘两级，此属性定义磁盘的缓存位置。参数解释如下：
         user.home – 用户主目录
         user.dir  – 用户当前工作目录
         java.io.tmpdir – 默认临时文件路径
       -->
      <diskStore path="java.io.tmpdir/Tmp_EhCache"/>
      <!--
         defaultCache：默认缓存策略，当ehcache找不到定义的缓存时，则使用这个缓存策略。只能定义一个。
       -->
      <!--
        name:缓存名称。
        maxElementsInMemory:缓存最大数目
        maxElementsOnDisk：硬盘最大缓存个数。
        eternal:对象是否永久有效，一但设置了，timeout将不起作用。
        overflowToDisk:是否保存到磁盘，当系统当机时
        timeToIdleSeconds:设置对象在失效前的允许闲置时间（单位：秒）。仅当eternal=false对象不是永久有效时使用，可选属性，默认值是0，也就是可闲置时间无穷大。
        timeToLiveSeconds:设置对象在失效前允许存活时间（单位：秒）。最大时间介于创建时间和失效时间之间。仅当eternal=false对象不是永久有效时使用，默认是0.，也就是对象存活时间无穷大。
        diskPersistent：是否缓存虚拟机重启期数据 Whether the disk store persists between restarts of the Virtual Machine. The default value is false.
        diskSpoolBufferSizeMB：这个参数设置DiskStore（磁盘缓存）的缓存区大小。默认是30MB。每个Cache都应该有自己的一个缓冲区。
        diskExpiryThreadIntervalSeconds：磁盘失效线程运行时间间隔，默认是120秒。
        memoryStoreEvictionPolicy：当达到maxElementsInMemory限制时，Ehcache将会根据指定的策略去清理内存。默认策略是LRU（最近最少使用）。你可以设置为FIFO（先进先出）或是LFU（较少使用）。
        clearOnFlush：内存数量最大时是否清除。
        memoryStoreEvictionPolicy:可选策略有：LRU（最近最少使用，默认策略）、FIFO（先进先出）、LFU（最少访问次数）。
        FIFO，first in first out，这个是大家最熟的，先进先出。
        LFU， Less Frequently Used，就是上面例子中使用的策略，直白一点就是讲一直以来最少被使用的。如上面所讲，缓存的元素有一个hit属性，hit值最小的将会被清出缓存。
        LRU，Least Recently Used，最近最少使用的，缓存的元素有一个时间戳，当缓存容量满了，而又需要腾出地方来缓存新的元素的时候，那么现有缓存元素中时间戳离当前时间最远的元素将被清出缓存。
     -->
  </ehcache>
  ```

  
