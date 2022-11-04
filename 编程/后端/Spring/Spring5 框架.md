# Spring5 框架

### 1\. Spring 框架概述

① Spring 是轻量级的开源的 JavaEE 框架

② Spring 可以解决企业应用开发的复杂性

③ Spring有两个核心部分：IOC 和 Aop

- IOC: 控制反转，把创建对象的过程交给 Spring 进行管理
- Aop: 面向切面，不修改源代码进行功能添加或增强

④ Spring 特点

1. 方便解耦，简化开发
2. Aop 编程支持
3. 方便程序测试
4. 方便和其他框架进行整合
5. 方便进行事务操作
6. 降低 API 开发难度

>轻量级：引入的依赖少，体积小、可以独立运行

### 2\.  入门案例

**第一步** 下载最新 GA 版本（release）

地址：`https://repo.spring.io/webapp/#/artifacts/browse/tree/General/release/org/springframework/spring`

或者通过创建 Maven 工程直接下载

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring</artifactId>
    <version>5.2.12.RELEASE</version>
    <type>pom</type>
</dependency>
```

> Java 工程笔记详看 Spring5 框架课堂笔记

**第二步** 创建 Maven 工程 略

**第三步** 在 pom.xml 中导入依赖 spring5 相关的包

```xml
<dependency>
    <groupId>commons-logging</groupId>
    <artifactId>commons-logging</artifactId>
    <version>1.2</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.2.12.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>5.2.12.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-beans</artifactId>
    <version>5.2.12.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-expression</artifactId>
    <version>5.2.12.RELEASE</version>
</dependency>
```

**第四步** 创建类，并在该类中创建一个普通方法

```java
package com.atguigu.maven;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class User {
    private static final Logger LOGGER = LoggerFactory.getLogger(User.class);
    public void add(){
        LOGGER.debug("add dfasdfasdfasdf");
    }
}
```

**第五步** resources 创建 spring 的配置文件

① spring 配置文件使用 xml 格式。resources/config 下创建 bean1.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
     <!--配置 User 对象创建--> 
    <bean id="user" class="com.atguigu.maven.User"/>
</beans>
```

**第六步** test 包下进行测试代码编写

```java
package com.atguigu.maven;

import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class TestUser {
    private static final Logger LOGGER = LogManager.getLogger(TestUser.class);
    @Test
    public void testAdd(){
        //1. 加载 Spring 的配置文件
        ApplicationContext context = new ClassPathXmlApplicationContext("config/bean1.xml");
        //FileSystemXmlApplicationContext 系统路径下 ClassPathXmlApplicationContext classpath 路径下
        User user = context.getBean("user", User.class);
        user.add();
    }
}

```

整体文件结构：

**![image-20210204231824077](C:%5CUsers%5CXJ%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20210204231824077.png)

![](image-20210220235239535.png)

### 3\. IOC 容器

##### ① IOC 底层原理

**IOC 概念和原理**

1\. 什么是 IOC？

Ⅰ 控制反转（ Inversion of Control，缩写为loc），把对象创建和对象之间的调用过程，交给 Spring 管理

Ⅱ 使用 IOC 的目的：为了耦合度降低。

2\. IOC 底层原理

xml 解析、工厂模式、反射

工厂模式和原始方式的对比

![图1](%E5%9B%BE1.png)

xml 和反射

![图2](%E5%9B%BE2.png)

##### ② IOC 接口 （BeanFactory）

1\. IOC 思想基于 IOC 容器，IOC 容器底层就是对象工厂

2\. Spring 提供 IOC 容器实现 两种方式（两个接口）：

- BeanFactory：IOC容器基本实现，是 Spring 内部的使用接口，不提供开发人员进行使用（也可以使用）

  加载配置文件时不会创建对象，在获取对象（使用）才去创建对象

- ApplicationContext：BeanFactory.接口的子接口，提供更多更强大的功能，一般由开发人员进行使用

  加载配置文件时就会把配置文件对象进行创建

ApplicationContext 接口的实现类:

- FileSystemXmlApplicationContext
- ClassPathXmlApplicationContext

**IOC 操作 Bean 管理**（概念）

1\. 什么是 Bean 管理？

Bean 管理指的是两个操作：

① Spring 创建对象

② Spring 注入属性

2\. Bean 管理操作的两种方式

① 基于 xml 配置文件方式实现

② 基于注解方式方式实现

4\. AOP

### 4\. IOC 操作 Bean 管理 （基于 xml）

##### IOC 操作 Bean 管理（基于 xml ）入门

1\. 基于 xml 方式创建对象

```xml
<!-- 配置User 对象创建-->
<bean name="user" class="com.atguigu.spring.User"/>
```

① 在 Spring 配置文件中，使用 bean 标签，标签里面添加相应属性，就可以实现对象创建

② bean 标签中的常用属性：

   - id 属性： 唯一标识（别名）
   - class 属性： 类全路径（包类全路径）
   - name 属性：作用和 id 一样，但是可以有特殊符号。如反斜杠。过时，用的很少

③ 创建对象时，默认执行无参构造器

 2\. 基于 xml 方式注入属性

DI：依赖注入，就是注入属性

3\. 第一种方式：使用 set 方法进行注入

① 创建类，对应的属性和对应 set 方法

```java
public class Book {

    //创建属性
    private String bname;

    public Book(String bname) {
        this.bname = bname;
    }

	//创建属性对应的 set 方法
    public void setBname(String bname) {
        this.bname = bname;
    }
    
    public void return Bname(String bname) {
        return bname;
    }
    @Test
    public void test1(){
        //set 方法注入
    	Book book = new Book();
         book.setBname("abc");
        //有参构造方法注入
        Book book = new Book("abc");
    }
}       
```

② 在 spring 配置文件配置对象创建，配置属性注入

```java
 <!-- set 方法注入属性 -->
    <bean id="book" class="com.atguigu.spring.Book">
    <!--使用 property 完成属性注入：
        name: 类里面属性名称
        value: 向属性注入的值
        -->
        <property name="bname" value="易筋经"></property>
    </bean>  
```

③ 在 Java 程序中获取对象

```java
ApplicationContext context = new ClassPathXmlApplicationContext("config/beans.xml");
        Book book = context.getBean("book", Book.class);
        System.out.println(book.getBname());
```

4\. 第二种注入方式：使用有参数构造方法进行注入

① 创建类，对应的属性和对应有参构造方法

③ 用有参构造

```xml
<!-- 有参数构造注入属性-->
<bean id="book" class="com.atguigu.spring.Book">
        <!--使用 constructor-arg 完成属性注入：
                    name: 类里面属性名称
                    value: 向属性注入的值
				   index: 有参构造中的第几个参数。（第二种写法，不常用，使用属性名称更加准确）从 0 开始计数
                    -->
    	<constructor-arg index="0" value="dfad"></constructor-arg>
        <constructor-arg name="bname" value="asd"></constructor-arg>
</bean>
```

##### IOC 操作 Bean 管理 （p 名称空间注入）

使用 p 名称空间注入，可以简化基于 xml 配置方式 

**第一步** 添加 p 名称空间在配置文件中

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
```

**第二步** 进行属性注入，在 bean 标签里面进行操作

```xml
<!-- set 方法注入属性 -->
<bean id="book" class="com.atguigu.spring.Book" p:bname="九阳神功" p:bauthor="无名氏"></bean>
格式：p:属性名称="属性值"
```

##### IOC 操作 Bean 管理 (xml 中注入其他类型属性)

###### 1\. 字面量

程序中一些写死的值。例如上面例子中配置的 bname 的属性值。

① null 值

```xml
<property name="address">
    <null/>
</property>
```

② 属性值中包含特殊符号

解决办法：

*第一种*：使用字符实体 例如：`&lt;&gt;`

*第二种*：把包含特殊符号的内容写到 CData 区域中

```xml
例如:
<property name="bname">
    <value><![CDATA[<<南京>>]]></value>
</property>
<!--格式：-->
<![CDATA[
不需要解析的内容
]]>
```

> 在 XHTML （XML）中 CData 区域是文档中的一个特殊区域，这个区域中可以包含不需要解析的任意格式的文本内容

###### 2\. 注入属性-外部 bean

（1）创建两个类 service 类和 dao 类

（2）在 service 中调用 dao 中的方法

（3）在 spring 配置文件中进行配置

```java
public interface UserDao {
    void update();
}

public class UserDaoImpl implements UserDao{

    public void update(){
        System.out.println("dao update-------");
    }

}
//可以先创建个接口然后再去实现
public class UserService {
    
    //创建 UserDao 类型属性，生成 set 方法
    private UserDao userDao;
    
    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }

    public void add(){
        System.out.println("service add---------------");
        userDao.update();
    }
    
}

在 spring 配置文件中
<bean id="userService" class="com.atguigu.spring.service.UserService">
    	<!-- ref 属性： 创建 userDao 对象 bean 标签对象值-->
        <property name="userDao" ref="userDaoImpl"></property>
</bean>
<bean id="userDaoImpl" class="com.atguigu.spring.dao.UserDaoImpl"></bean>
```

###### 3\. 注入属性-内部 bean 和级联赋值

（1）一对多关系：部门和员工一个部门有多个员工，一个员工属于一个部门

​	部门是一，员工是多

（2）在实体类之间表示一对多关系，员工表示所属部门，使用对象类型属性进行表示

```java
//部门
public class Department {

    private String dname;

    public void setDname(String dname) {
        this.dname = dname;
    }

}
//员工
public class Employee {

    private String ename;
    private String gender;
    // 员工属于某一个部门,用对象表示
    private Department department;

    public void setEname(String ename) {
        this.ename = ename;
    }

    public void setGender(String gender) {
        this.gender = gender;
    }

    public void setDepartment(Department department) {
        this.department = department;
    }
}
```

（3）在 Spring 文件中进行配置

```xml
<bean id="employee" class="com.atguigu.spring.Employee">
        <property name="ename" value="lucy"></property>
        <property name="gender" value="女"></property>
        <!--设置对象类型属性-->
        <property name="department">
            <bean id="department" class="com.atguigu.spring.Department">
                <property name="dname" value="安保部"></property>
            </bean>
        </property>
    </bean>
<!-- 当然，也可以像上面的那个例子那样，使用外部 bean。外部 bean 和内部 bean 效果是一样的 -->
上面的例子就是级联赋值。
级联赋值的其他写法：
<!--第一种-->
<bean id="employee" class="com.atguigu.spring.Employee">
        <property name="ename" value="lucy"></property>
        <property name="gender" value="女"></property>
        <!--级联赋值-->
        <property name="department" ref="department"></property>
</bean>
<bean id="department" class="com.atguigu.spring.Department">
         <property name="dname" value="安保部"></property>
</bean>
<!--第二种-->
<bean id="employee" class="com.atguigu.spring.Employee">
        <property name="ename" value="lucy"></property>
        <property name="gender" value="女"></property>
        <!--级联赋值-->
        <property name="department" ref="department"></property>
   		<property name="department.dname" value="技术部"></property>
    	
</bean>
<bean id="department" class="com.atguigu.spring.Department">
</bean>

这种写法需要写 department.dname 的 get 方法
```

##### IOC 操作 Bean 管理（ 注入集合类型属性）

###### 1\. 注入数组类型属性

###### 2\. 注入 List 集合

###### 3\. 注入 Map 集合

第一步 创建类、定义数组、List、Map、Set 类型属性，并生成对应 set 方法

```java
public class Student {
    //1. 数组类型属性
    private String[] courses;
    //2. List 集合类型属性
    private List<String> list;
    //3. Map 集合类型属性
    private Map<String,String> map;
    //4. set 集合类型属性
    private Set<String> sets;
     //学生所学多门课程
    private List<Course> coursesList;

    public void setCoursesList(List<Course> coursesList) {
        this.coursesList = coursesList;
    }

    public void setCourses(String[] courses) {
        this.courses = courses;
    }

    public void setList(List<String> list) {
        this.list = list;
    }

    public void setMap(Map<String, String> map) {
        this.map = map;
    }

    public void setSets(Set<String> sets) {
        this.sets = sets;
    }
}
```

第二步 spring 配置文件进行配置

```xml
<bean id="student" class="com.atguigu.spring.Student">
    <!-- 数组类型属性注入 -->
    <property name="courses">
        <array>
            <value>Java 课程</value>
            <value>数据库课程 </value>
        </array>
    </property>
    <!-- list 类型属性注入-->
    <property name="list">
        <list>
            <value>张三</value>
            <value>王五</value>
        </list>
    </property>
    <!-- map 类型属性注入-->
    <property name="map">
        <map>
            <entry key="Java" value="java"></entry>
            <entry key="PHP" value="php"></entry>
        </map>
    </property>
    <!-- set 类型属性注入-->
    <property name="sets">
        <set>
            <value>MySQL</value>
            <value>Redis</value>
        </set>
    </property>
</bean>
```

在集合里面设置对象类型值

```xml
第一步创建多个 bean 标签
<!-- 创建多个 course 对象 -->
<bean id="course1" class="com.atguigu.spring.Course">
    <property name="cname" value="Spring5 框架课"></property>
</bean>
<bean id="course2" class="com.atguigu.spring.Course">
    <property name="cname" value="MyBatis 框架课"></property>
</bean>

<!-- list 集合类型属性注入，值是对象-->
<property name="coursesList">
    <list>
        <ref bean="course1"></ref>
        <ref bean="course2"></ref>
    </list>
</property>
```

###### 4\. 把集合注入部分提取出来

在 spring 配置文件中引入空间名称 util

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:util="http://www.springframework.org/schema/util"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util.xsd">
```

定义类、创建属性以及对应的 set 的方法

```java
public class Book {

	private List<String> list;
	
	public void setList(List<String> list) {
        this.list = list;
    }
    
}
```

在 Spring 中文件的配置

```xml
<!--提取 List 集合类型属性注入-->
<util:list id="booklist">
    <value>易筋经</value>
    <value>九阳真经</value>
    <value>九阳神功</value>
</util:list>
<!--提取List集合类型属性注入使用-->
<bean id="book" class="com.atguigu.spring.Book">
	<property name="list" ref="booklist"></property>
</bean>
```

##### IOC 操作 Bean 管理 (FactoryBean)

1\. Spring 有两种类型 bean，一种普通 bean，另一种工厂 Bean （FactoryBean）

2\. 普通 Bean

在配置文件中定义 bean 类型就是返回类型

3\. 工厂 Bean

在配置文件中定义 bean 类型可以返回类型不一致

**第一步** 创建类，实现 FactoryBean 接口，让这个类作为工厂 Bean

**第二步** 实现接口里面的方法，在实现的方法中定义返回的 Bean 类型

```java
public class MyBean implements FactoryBean<Course> {

	//感觉这一步创建对象交给 Spring 处理可能会更好
    @Override
    public Course getObject() throws Exception {
        Course course = new Course();
        course.setCname("abc");
        return course;
    }

    @Override
    public Class<?> getObjectType() {
        return null;
    }

    @Override
    public boolean isSingleton() {
        return false;
    }
}
//Spring 配置文件中的设置
<bean id="myBean" class="com.atguigu.spring.facbean.MyBean"></bean>
//测试：
ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml"); 
Course course = context.getBean("myBean",Course.class);
System.out.pringln(course);
```

##### IOC 操作 Bean 管理 （Bean 作用域）

1\. 在 Spring 里面，创建 Bean 实例，设置是单实例或多实例。

2\. 在 Spring 里面，默认情况下，bean 是单实例对象

3\. 如何设置单实例还是多实例

（1）Spring配置文件 bean 标签里面有属性 (scope) 用于设置单实例还是多实例

（2）scope 属性值

- 第一个值 默认值，Singleton，表示是单实例对象
- 第二个值 prototype，表示是多实例对象

```xml
<bean id="book" class="com.atguigu.spring.Book" scope="prototype">
    <property name="list" ref="booklist"></property>
</bean>
```

（3）singleton 和 prototype 区别

① singleton 单实例，prototype 多实例

② 设置 scope 值是 singleton 时，加载 spring 配置文件就会创建单实例对象

​	设置 scope 值是 prototype 时，不会在加载 spring 配置文件就会创建单实例对象，而是在调用 getBean() 方法时创建多实例对象

③ 其他值 request （请求），session (会话)

> 建议使用默认的 singleton

##### IOC 操作 Bean 管理 （Bean 生命周期）

1\. 生命周期

 从对象创建到销毁的过程

2\. bean 声明周期

① 通过构造器创建 bean  实例（无参构造方法）

② 为 bean 的属性设置值和对其他 bean 引用（使用 set 方法，属性注入）

③ 调用 bean 初始化的方法（需要进行配置初始化的方法）

④ bean 可以使用了（对象获取到了）

⑤ 当容器关闭时，调用 bean 的销毁方法（需要进行配置销毁）

3\. 代码示例

```java
public class Orders {

    private static final Logger LOGGER = LoggerFactory.getLogger(Orders.class);
    private String name;

    public Orders() {
            LOGGER.debug("第一步 执行无参数构造创建 bean 实例");
    }

    public void setName(String name) {
        this.name = name;
        LOGGER.debug("第二步 调用 set 方法设置属性值");
    }

    //创建执行的初始化的方法
    public void initMethod(){
        LOGGER.debug("第三步 执行初始化的方法");
    }

    //创建执行的销毁方法
    public void destoryMethod(){
        LOGGER.debug("第五步 执行的销毁方法");
    }


}
//spring 文件配置
<bean id="orders" class="com.atguigu.spring.bean.Orders" init-method="initMethod" destroy-method="destoryMethod">
    <property name="name" value="手机"></property>
</bean>

//测试代码

ApplicationContext context = new ClassPathXmlApplicationContext("config/beans.xml");
Orders orders = context.getBean("orders", Orders.class);
LOGGER.debug("第四步 获取创建 bean 实例对象");
LOGGER.info(orders.toString());
//手动让 bean 实例销毁
((ClassPathXmlApplicationContext)context).close();

//输出结果
2021-02-24 19:00:41,058 DEBUG (com.atguigu.spring.bean.Orders.<init>:16)  - 第一步 执行无参数构造创建 bean 实例
2021-02-24 19:00:41,059 DEBUG (com.atguigu.spring.bean.Orders.setName:21)  - 第二步 调用 set 方法设置属性值
2021-02-24 19:00:41,059 DEBUG (com.atguigu.spring.bean.Orders.initMethod:26)  - 第三步 执行初始化的方法
2021-02-24 19:00:41,066 DEBUG (com.atguigu.spring.OrdersTest.test0:22)  - 第四步 获取创建 bean 实例对象
2021-02-24 19:00:41,066  INFO (com.atguigu.spring.OrdersTest.test0:23)  - com.atguigu.spring.bean.Orders@1af687fe
2021-02-24 19:00:41,071 DEBUG (org.springframework.context.support.AbstractApplicationContext.doClose:1006)  - Closing org.springframework.context.support.ClassPathXmlApplicationContext@2438dcd, started on Wed Feb 24 19:00:40 JST 2021
2021-02-24 19:00:41,071 DEBUG (com.atguigu.spring.bean.Orders.destoryMethod:31)  - 第五步 执行的销毁方法
```

4\. bean 的后置处理器，bean 的声明周期有七步：

① 通过构造器创建 bean  实例（无参构造方法）

② 为 bean 的属性设置值和对其他 bean 引用（使用 set 方法，属性注入）

**③** 把 bean 的实例传递给 bean 后置处理器的方法 postProcessBeforeInitialization()

④ 调用 bean 初始化的方法（需要进行配置初始化的方法）

**⑤** 把 bean 的实例传递给 bean 后置处理器的方法 postProcessAfterInitialization()

⑥ bean 可以使用了（对象获取到了）

⑦ 当容器关闭时，调用 bean 的销毁方法（需要进行配置销毁）

5\. 演示后置处理器的效果

① 创建类，实现接口 BeanPostProcessor，创建后置处理器

```xml
public class MyBeanPost implements BeanPostProcessor {

    private static final Logger LOGGER = LoggerFactory.getLogger(MyBeanPost.class);

    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        LOGGER.debug("在初始化之前执行的方法");
        return bean;
    }

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        LOGGER.debug("在初始化之后执行的方法");
        return bean;
    }
}
//spring 配置文件中的配置
<!-- 配置后置处理器 -->
    <bean id="myBeanPost" class="com.atguigu.spring.bean.MyBeanPost"></bean>
//输出结果
2021-02-24 19:17:02,059 DEBUG (com.atguigu.spring.bean.Orders.<init>:16)  - 第一步 执行无参数构造创建 bean 实例
2021-02-24 19:17:02,060 DEBUG (com.atguigu.spring.bean.Orders.setName:21)  - 第二步 调用 set 方法设置属性值
2021-02-24 19:17:02,060 DEBUG (com.atguigu.spring.bean.MyBeanPost.postProcessBeforeInitialization:19)  - 在初始化之前执行的方法
2021-02-24 19:17:02,060 DEBUG (com.atguigu.spring.bean.Orders.initMethod:26)  - 第三步 执行初始化的方法
2021-02-24 19:17:02,061 DEBUG (com.atguigu.spring.bean.MyBeanPost.postProcessAfterInitialization:25)  - 在初始化之后执行的方法
2021-02-24 19:17:02,069 DEBUG (com.atguigu.spring.OrdersTest.test0:22)  - 第四步 获取创建 bean 实例对象
2021-02-24 19:17:02,069  INFO (com.atguigu.spring.OrdersTest.test0:23)  - com.atguigu.spring.bean.Orders@74a6a609
2021-02-24 19:17:02,075 DEBUG (org.springframework.context.support.AbstractApplicationContext.doClose:1006)  - Closing org.springframework.context.support.ClassPathXmlApplicationContext@2438dcd, started on Wed Feb 24 19:17:01 JST 2021
2021-02-24 19:17:02,076 DEBUG (com.atguigu.spring.bean.Orders.destoryMethod:31)  - 第五步 执行的销毁方法
```

**IOC 操作 Bean 管理 （xml 自动装配)**

1\. 什么是自动装配？

① 根据指定装配规则（属性名称或属性类型），Spring 自动将匹配的属性值进行注入。

2\. 代码演示自动装配

```java
public class Department {

    @Override
    public String toString() {
        return "Department{}";
    }
}

public class Employee {

    private Department department;

    public void setDepartment(Department department) {
        this.department = department;
    }

    @Override
    public String toString() {
        return "Employee{" +
                "department=" + department +
                '}';
    }

    public void test(){
        System.out.println(department);
    }
}

    <!-- 实现自动装配:
    bean 标签属性 autowire，配置自动装配
    qutowire 属性常用两个值：
        - byName 根据属性名称注入，**注入值 bean 的 id 值和类属性名称一样**
        - byTeype 根据属性类型注入，如果使用 byType，相同类型的 Bean 不能定义多个
     -->
    <bean id="employee" class="com.atguigu.spring.autowire.Employee" autowire="byName">
        <!--   之前的写法    <property name="department" ref="department"></property>-->
    </bean>
    <bean id="department" class="com.atguigu.spring.autowire.Department">
    </bean>

               ApplicationContext context = new ClassPathXmlApplicationContext("config/beans.xml");
        Employee employee = context.getBean("employee", Employee.class);
        LOGGER.debug("第四步 获取创建 bean 实例对象");
        LOGGER.info(employee.toString());
        //手动让 bean 实例销毁
        ((ClassPathXmlApplicationContext)context).close();
2021-02-24 19:58:45,280  INFO (com.atguigu.spring.OrdersTest.test0:23)  - Employee{department=Department{}}
```

IOC 操作 Bean 管理（外部属性文件）

1\. 直接配置数据库信息

（1） 配置德鲁伊连接池

spring xml 配置：

```xml
<!--直接配置连接池-->
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
     <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
     <property name="url"
    value="jdbc:mysql://localhost:3306/userDb"></property>
     <property name="username" value="root"></property>
     <property name="password" value="root"></property>
</bean>
```

（2）引入德鲁伊连接池依赖 jar 包

```xml
<!--引入 druid jar包-->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.2.5</version>
</dependency> 
```

2\. 引入外部属性文件配置数据库连接池

（1）创建外部属性文件，properties 格式文件，写数据库信息

```properties
prop.driverClass=com.mysql.jdbc.Driver
prop.url=jdbc://mysql://localhost:3306/userDb
prop.userName=root //用户名
prop.password=root //密码
```

（2）把外部 properties 属性文件引入到 spring 配置文件中

第一步： 引入 context 名称空间 

```xml
<!---->
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:util="http://www.springframework.org/schema/util"
       xmlns:context="http://www.springframework.org/schema/context" 
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
 						 http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
```

第二步：在 spring 配置文件使用标签引入外部属性文件

```xml
<context:property-placeholder location="classpath:jdbc.properties"/>
<!--配置连接池-->
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
     <property name="driverClassName" value="${prop.driverClass}"></property>
     <property name="url" value="${prop.url}"></property>
     <property name="username" value="${prop.userName}"></property>
     <property name="password" value="${prop.password}"></property>
</bean>
```

### 5\.   IOC 操作  Bean 管理 （基于注解的方式）

1\. 什么是注解 

① 注解是代码特殊标记，格式：@注解名称(属性名称=属性值, 属性名称=属性值..)

② 使用注解，注解作用在类上面，方法上面，属性上面 

③ 使用注解目的：简化 xml 配置

2\. Spring 针对 Bean 管理中创建对象提供注解 

（1）@Component 

（2）@Service 

（3）@Controller 

（4）@Repository 

Tips：上面四个注解功能是一样的，都可以用来创建 bean **实例**

3\. 基于注解的方式实现对象创建

第一步 引入依赖

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aop</artifactId>
    <version>${spring.version}</version>
</dependency>
```

第二步 开启组件扫描

```xml
<!--开启组件扫描
 1 如果扫描多个包，多个包使用逗号隔开
 2 扫描包上层目录
-->

<context:component-scan base-package="com.atguigu"></context:component-scan>
```

第三步  创建类，在类上添加创建对象注解

```java
//在注解里面 value 属性值可以省略不写，
//默认值是类名称，首字母小写
@Component(value = "userService") //<bean id="userService" class=".."/>
public class UserService {
     public void add() {
     System.out.println("service add.......");
     }
}
```

4\. 开启组件扫描细节配置

```xml
<!--示例 1
 use-default-filters="false" 表示现在不使用默认 filter，自己配置 filter
 context:include-filter ，设置扫描哪些内容
-->
<context:component-scan base-package="com.atguigu" use-defaultfilters="false">
 <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
</context:component-scan>
<!--示例 2
 下面配置扫描包所有内容
 context:exclude-filter： 设置哪些内容不进行扫描
-->
<context:component-scan base-package="com.atguigu">
 <context:exclude-filter type="annotation"
expression="org.springframework.stereotype.Controller"/>
</context:component-scan>
```

5、基于注解方式实现属性注入 

（1）@Autowired：根据属性类型进行自动装配 

第一步 把 service 和 dao 对象创建，在 service 和 dao 类添加创建对象注解 

第二步 在 service 注入 dao 对象，在 service 类添加 dao 类型属性，在属性上面使用注解

```java
@Service
public class UserService {
 //定义 dao 类型属性
 //不需要添加 set 方法
 //添加注入属性注解
 @Autowired
 private UserDao userDao;
 public void add() {
 System.out.println("service add.......");
 userDao.add();
 }
}

```

（2）@Qualifier：根据属性名称进行注入 

这个@Qualifier 注解的使用，和上面@Autowired 一起使用

```java
//定义 dao 类型属性
//不需要添加 set 方法
//添加注入属性注解
@Autowired //根据类型进行注入
@Qualifier(value = "userDaoImpl1") //根据名称进行注入
private UserDao userDao;

```

（3）@Resource：可以根据类型注入，可以根据名称注入

```java
//@Resource //根据类型进行注入
@Resource(name = "userDaoImpl1") //根据名称进行注入
private UserDao userDao;
```

（4）@Value：注入普通类型属性

```java
@Value(value = "abc")
private String name;
```

6\. 完全注解开发

（1）创建配置类，替代 xml 配置文件 

```java
@Configuration //作为配置类，替代 xml 配置文件
@ComponentScan(basePackages = {"com.atguigu"}) 
public class SpringConfig { } 
```

（2）编写测试类

```java
@Test
public void testService2() {
 //加载配置类
 ApplicationContext context = new AnnotationConfigApplicationContext(SpringConfig.class);
 UserService userService = context.getBean("userService",UserService.class);
 System.out.println(userService);
 userService.add();
}
```

### 6\. AOP

#### AOP （概念）

1\. 什么是 AOP ？

（1）面向切面编程（方面），利用 AOP 可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。 

（2）通俗描述：不通过修改源代码方式，在主干功能里面添加新功能 

（3）使用登录例子说明 AOP

![图3](%E5%9B%BE3.png)

#### AOP （底层原理）

1\. AOP 底层使用动态代理

底层使用动态代理有两种情况：

**第一种** 有接口情况，使用 JDK 动态代理 

创建接口实现类代理对象，增强类的方法

**第二种** 没有接口情况，使用 CGLIB 动态代理

创建子类的代理对象，增强类的方法

![image-20210225231930657](image-20210225231930657.png)

#### AOP（JDK 动态代理）

1\. 使用 JDK 动态代理，使用 Proxy 类里面的方法创建代理对象

`java.lang.reflect.Proxy`

① 调用 newProxyInstance() 方法

|     类型      |                             方法                             |                             描述                             |
| :-----------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
| static object | newProxyInstance(ClassLoader loader,class<?>[] interfaces,InvocationHandler h) | 返回指定接口的代理类实例，该接口将方法调用分配给指定的调用调用处理程序 |

方法有三个参数：

- 第一参数，类加载器 
- 第二参数，增强方法所在的类，这个类实现的接口，支持多个接口 
- 第三参数，实现这个接口 InvocationHandler，创建代理对象，写增强的部分

2\. 编写 JDK 动态代码实例

第一步 创建接口，定义方法

```java
public interface UserDao {
 public int add(int a,int b);
 public String update(String id);
}
```

第二步 创建接口实现类，实现方法

```java
public class UserDaoImpl implements UserDao {
 @Override
 public int add(int a, int b) {
 return a+b;
 }
 @Override
 public String update(String id) {
 return id;
 }
}
```

第三步 使用 Proxy 类创建接口代理对象

```java
public class JDKProxy {
     public static void main(String[] args) {
         //创建接口实现类代理对象
         Class[] interfaces = {UserDao.class};
        // Proxy.newProxyInstance(JDKProxy.class.getClassLoader(), interfaces,new InvocationHandler() {
        // @Override
        // public Object invoke(Object proxy, Method method, Object[] args)throws Throwable {
        // return null;
        // }
        // });
         UserDaoImpl userDao = new UserDaoImpl();
         UserDao dao =(UserDao)Proxy.newProxyInstance(JDKProxy.class.getClassLoader(), interfaces,new UserDaoProxy(userDao));
         int result = dao.add(1, 2);
         System.out.println("result:"+result);
         }
    	}
    //创建代理对象代码
    class UserDaoProxy implements InvocationHandler {
     //1 把创建的是谁的代理对象，把谁传递过来
     //有参数构造传递
     private Object obj;
     public UserDaoProxy(Object obj) {
     this.obj = obj;
     }
     //增强的逻辑
     @Override
     public Object invoke(Object proxy, Method method, Object[] args) throws
    Throwable {
     //方法之前
     System.out.println("方法之前执行...."+method.getName()+" :传递的参
    数..."+ Arrays.toString(args));
     //被增强的方法执行
     Object res = method.invoke(obj, args);
     //方法之后
     System.out.println("方法之后执行...."+obj);
     return res;
     }
}

```

#### AOP  术语

- **连接点** 类里面那些方法可以被增强，这些方法称为连接点

- **切入点** 实际被真正增强的方法，称为切入点

- **通知（增强）** 

  - 实际增强的逻辑部分称为通知（增强）
  - 通知有多种类型、
    1. 前置通知
    2. 后置通知
    3. 环绕通知
    4. 异常通知
    5. 最终通知

- **切面** 切面是动作；把通知应用到切入点过程

#### AOP 操作（准备工作）

1\. Spring 框架一般都是基于 AspectJ 实现 AOP 操作

AspectJ 不是 Spring 组成部分，独立 AOP 框架，一般把 AspectJ 和 Spirng 框架一起使用，进行 AOP 操作

2\. 基于 AspectJ 实现 AOP 操作

①  基于 xml 配置文件实现 

② 基于注解方式实现（使用）

3\. 在项目工程中导入相关依赖

```java
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aspects</artifactId>
    <version>${spring.version}</version>
</dependency>
<dependency>
    <groupId>cglib</groupId>
    <artifactId>cglib</artifactId>
    <version>3.3.0</version>
</dependency>
<dependency>
    <groupId>aopalliance</groupId>
    <artifactId>aopalliance</artifactId>
    <version>1.0</version>
</dependency>
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.6</version>
</dependency>
```

 4\. 切入点表达式

① 切入点表达式的作用：知道对哪个类里面的哪个方法进行增强

② 语法结构： `execution([权限修饰符] [返回类型] [类全路径] [方法名称]([参数列表]) )` 举例：

举例 1：对 com.atguigu.dao.BookDao 类里面的 add 进行增强 

`execution(* com.atguigu.dao.BookDao.add(..)) `

Tips: 这里的 * 代表任意权限；这个例子也将返回类型省略了。

举例 2：对 com.atguigu.dao.BookDao 类里面的所有的方法进行增强 

`execution(* com.atguigu.dao.BookDao.* (..))`

举例 3：对 com.atguigu.dao 包里面所有类，类里面所有方法进行增强 

`execution(* com.atguigu.dao.*.* (..))`

#### AOP 操作 （AspectJ 注解）

1\. 创建类，在类里面定义方法

```java
public class User {
 public void add() {
 System.out.println("add.......");
 }
}
```

2\. 创建增强类 （编写增强逻辑）

（1）在增强类里面，创建方法，让不同方法代表不同通知类型

```java
//增强的类
public class UserProxy {
 public void before() {//前置通知
 System.out.println("before......");
 }
}
```

3\. 进行通知的配置

（1） 在 Spring 配置文件中，开启注解扫描

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xmlns:context="http://www.springframework.org/schema/context"
 xmlns:aop="http://www.springframework.org/schema/aop"
 xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
 				    http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
                     http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
 <!-- 开启注解扫描 -->
 <context:component-scan basepackage="com.atguigu.spring5.aopanno"></context:component-scan>
```

(2)  创建 User 和 UserProxy 对象

```java
//被增强的类
@Component
public class User{}
//增强的类
@Component
public class UserProxy{}
```

(3) 在增强类的上面添加注解 @Aspect

````java
//增强的类
@Component
@Aspect //生成代理对象
public class UserProxy{}
````

(4) 在 spring 配置文件中开启生成代理对象

```xml
<!--开启 Aspect 生成代理对象-->
<aop:aspectj-autoproxy></aop:aspectj-autoproxy>
```

4\. 配置不同类型的通知

在增强类的里面，在作为通知方法上面添加通知类型注解，使用切入点表达式配置

```java
//增强的类
@Component
@Aspect //生成代理对象
public class UserProxy {
 //前置通知
 //@Before 注解表示作为前置通知
 @Before(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")
 public void before() {
 System.out.println("before.........");
 }
 //后置通知（返回通知）
 @AfterReturning(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")
 public void afterReturning() {
 System.out.println("afterReturning.........");
 }
 //最终通知
 @After(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")
 public void after() {
 System.out.println("after.........");
 }
 //异常通知
 @AfterThrowing(value = "execution(*
com.atguigu.spring5.aopanno.User.add(..))")
 public void afterThrowing() {
 System.out.println("afterThrowing.........");
 }
 //环绕通知
 @Around(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")
 public void around(ProceedingJoinPoint proceedingJoinPoint) throws
Throwable {
 System.out.println("环绕之前.........");
 //被增强的方法执行
 proceedingJoinPoint.proceed();
 System.out.println("环绕之后.........");
 }
}
```

5\. 相同切入点的抽取

```java
//相同切入点抽取
@Pointcut(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")
public void pointdemo() {
}
//前置通知
//@Before 注解表示作为前置通知
@Before(value = "pointdemo()")
public void before() {
 System.out.println("before.........");
}
```

6\. 有多个增强类同一个方法进行增强

```java
（1）在增强类上面添加注解 @Order(数字类型值)，数字类型值越小优先级越高
@Component
@Aspect
@Order(1)
public class PersonProxy
```

7\. 完全使用注解开发

```java
@Configuration
@ComponentScan(basePackages = {"com.atguigu"})
@EnableAspectJAutoProxy(proxyTargetClass = true)
public class ConfigAop {
}
```

#### AOP 操作 (AsecptJ 配置文件)

1\.创建两个类，增强类和被增强类，创建方法

2\. 在 spring 配置文件中创建两个类对象

```xml
<!--创建对象-->
<bean id="book" class="com.atguigu.spring5.aopxml.Book"></bean>
<bean id="bookProxy" class="com.atguigu.spring5.aopxml.BookProxy"></bean>
```

3\.  在 spring 配置文件中配置切入点

```xml
<!--配置 aop 增强-->
<aop:config>
 <!--切入点-->
 <aop:pointcut id="p" expression="execution(*
com.atguigu.spring5.aopxml.Book.buy(..))"/>
 <!--配置切面-->
 <aop:aspect ref="bookProxy">
 <!--增强作用在具体的方法上-->
 <aop:before method="before" pointcut-ref="p"/>
 </aop:aspect>
</aop:config>
```

#### JDBCTemplate (概念和准备)

1\. 什么是 JdbcTemplate

Spring 框架对 JDBC 进行封装，使用 JdbcTemplate 方便实现对数据库操作

2\. 准备工作，

第一步 引入相关 jar 包

```xml
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.2.5</version>
        </dependency>
		<dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.23</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-orm</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-tx</artifactId>
            <version>${spring.version}</version>
        </dependency>
```

第二步 在 spring 配置文件配置数据库连接池

```xml
<!-- 数据库连接池 -->
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource"
 destroy-method="close">
     <property name="url" value="jdbc:mysql:///user_db" />
     <property name="username" value="root" />
     <property name="password" value="root" />
     <property name="driverClassName" value="com.mysql.jdbc.Driver" />
</bean>	
```

第三步 配置 JdbcTemplate 对象，注入 DataSource

```xml
<!-- JdbcTemplate 对象 -->
<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
 <!--注入 dataSource-->
 <property name="dataSource" ref="dataSource"></property>
</bean>
```

第四步 创建 service 类，创建 dao 类，在 dao 注入 jdbcTemplate 对象

\* 配置文件

```xml
<!-- 组件扫描 -->
<context:component-scan base-package="com.atguigu"></context:component-scan>
```

```java
@Service
public class BookService {
 //注入 dao
 @Autowired
 private BookDao bookDao;
}
@Repository
public class BookDaoImpl implements BookDao {
 //注入 JdbcTemplate
 @Autowired
 private JdbcTemplate jdbcTemplate;
}

```

#### JdbcTemplate 操作数据库（添加）

1\. 对应数据库创建实体类

```java
public class User {
    
    private String userId;
    private String userName;
    private String ustatus;

    public void setUserId(String userId) {
        this.userId = userId;
    }

    public void setUserName(String userName) {
        this.userName = userName;
    }

    public void setUstatus(String ustatus) {
        this.ustatus = ustatus;
    }
}
```

2\. 编写 service 和 dao 

（1）在 dao 进行数据库添加操作 

（2）调用 JdbcTemplate 对象里面 update 方法实现添加操作

`update(String sql,Object... args)`

有两个参数 :

- 第一个参数 sql 语句
- 第二个参数：可变参数，设置 sql 语句值

```java
@Repository
public class BookDaoImpl implements BookDao {
     //注入 JdbcTemplate
     @Autowired
     private JdbcTemplate jdbcTemplate;
     //添加的方法
     @Override
     public void add(Book book) {
     //1 创建 sql 语句
     String sql = "insert into t_book values(?,?,?)";
     //2 调用方法实现
     Object[] args = {book.getUserId(), book.getUsername(),
    book.getUstatus()};
     int update = jdbcTemplate.update(sql,args);
     System.out.println(update);
 }
}
```

#### 事务操作（事务概念）

1\. 什么事务?

① 事务是数据库操作最基本单元，逻辑上一组操作，要么都成功，如果有一个失败所有操 作都失败

② 典型场景：银行转账

2\. 事务四个特性（ACID）

- 原子性 

- 一致性 

- 隔离性 

- 持久性

#### 事务操作（搭建事务操作环境）

#### 事务操作（Spring 事务管理介绍）

#### 事务操作（注解声明式事务管理）

#### 事务操作（声明式事务管理参数配置）