# Spring---致力于简化Java开发

>  -Rod Johnson创建
>
> 4种关键策略:
>
> - 基于POJO的**轻量级**和**最小侵入性**编程
> - 通过**依赖注入**和**面向接口**实现**松耦合**
> - 基于**切面**和**惯例**进行**声明式**编程
> - 通过切面和模板**减少样板式代码**
>
> **核心特性**:
>
> - ==依赖注入==(dependency injection,DI)
> - ==面向切面编程==(aspect oriented programming,AOP)                         

## 第1部分    Spring核心

### 第1章   Spring之旅

#### 1.1.1  依赖注入(DI)

​	最大收益---**松耦合**。如果对象**间通过接口来表明依赖关系**---即**依赖于抽象**而不是具体实现，那么这种依赖能够在对象本身毫不知情的情况下，用不同的具体实现进行替换。

**装配:**

- XML装配

  ```xml
  <bean id="knight" class="com.springinaction.knights.BraveKnight">
  <constructor-arg ref="quest"/>
  </bean>-------注入Quest bean
  
  <bean id="quest" class="com.springinaction.knights.SlayDragonQuest">
  <constructor-arg value="#{T(System).out}"/>
  </bean>  -----创建SlayDragonQuest  #{}Spring表达式语言（SEL),将System.out(PrintStream)传入
  ```

- 基于Java配置  

  ```java
  @Configuration
  public class KnightConfig{
      
      @Bean
      public Knight knight(){
          return new BraveKnight(quest());
      }
      
      @Bean
      public Quest quest(){
          return new SlayDragonQuest(System.out);
      }
  }
  ```

#### 1.1.2 应用切面（面向切面编程AOP)

- 把遍布应用各处的功能分离出来形成可重用的组件；确保POJO的简单性。

  > 详细学习见后续章节；

#### 1.1.3 使用模板消除样板式代码

- 让代码关注于自身的职责;     **Template**

#### 1.2  Spring容器(Spring核心)

> 基于Spring的应用中,应用对象生存于 Spring容器(container)中；
>
> Spring容器负责创建对象,装配它们,配置它们并管理它们的整个生命周期,从生存到死亡(可能就是new 到 finalize())；如图:

![容器](images\容器.jpg)

**容器---Spring核心，自带容器归为两种类型:**

- ==bean工厂(`org.springframework.beans.factory.BeanFactory`接口定义)最简单的容器，提    供基本的DI支持。==

- ==**应用上下文(`org.springframework.context.ApplicationContext`接口定义)基于BeanFactory创建，并提供应用级别框架服务。**==


##### 1.2.1  ApplicationContext

`AnnotationConfigApplicationContext`：从一个或多个基于Java的配置类中加载Spring应用上下文。

`AnnotationConfigWebApplicationContext`：从一个或多个基于Java的配置类中加载Spring Web应用上下文。

`ClassPathXmlApplicationContext`：从类路径下的一个或多个XML配置文件中加载上下文定义，把应用上下文的定义文件作为类资源。

`FileSystemXmlApplicationContext`：从文件系统下的一个或多个XML配置文件中加载上下文定义。

`XmlWebApplicationContext`：从Web应用下的一个或多个XML配置文件中加载上下文定义。

```java
//加载一个FileSystemXmlApplicationContext
ApplicationContext context=
new FileSystemXmlApplicationContext("c：/knight.xml");
//加载一个ClassPathXmlApplicationContext
ApplicationContext context=
new ClassPathXmlApplicationContext("knight.xml");
//从Java配置中加载应用上下文
ApplicationContext context=
new AnnotationConfigApplicationContext(com.springinaction.knights.config.KnightConfig.class);
```

##### 1.2.2   bean的生命周期

![bean生命周期](E:\Java进阶(note)\Spring实战(第4版)\images\bean生命周期.jpg)

**详细描述:**

> 1.Spring对bean进行实例化;
>
> 2.Spring将值和bean的引用注入到bean对应的属性中；
>
> 3.如果bean实现了`BeanNameAware`接口，Spring将bean的ID传递给`setBeanName()`方法；
>
> 4.如果bean实现了`BeanFactoryAware`接口，Spring将调用`setBeanFactory()`,将`BeanFactory`容器实例传入；
>
> 5.如果bean实现了`ApplicationContextAware`接口，Spring将调用`setApplicationContext()`方法,将bean所在的应用上下文的引用传入进来；
>
> 6.如果bean实现了`BeanPostProcessor`接口，Spring将调用它们的`postProcessBeforeInitialization()`方法；
>
> 7.如果bean实现了`InitializingBean`接口，Spring将调用它们的`afterPropertiesSet()`方法。类似地，如果bean使用`init-method`声明了初始化方法，该方法也会被调用；
>
> 8.如果bean实现了`BeanPostProcessor`接口，Spring将调用它们的`postProcessAfterInitialization()`方法；
>
> 9.此时bean已经准备就绪，可被应用程序使用了，它们将一直驻留在应用上下文中，直到该应用上下文被销毁；
>
> 10.如果bean实现了`DisposableBean`接口，Spring将调用它的`destroy()`接口方法。同样如果bean使用`destroy-method`声明了销毁方法，该方法也会被调用；

##### 1.2.3  Spring模块

![Spring模块](E:\Java进阶(note)\Spring实战(第4版)\images\Spring模块.jpg)

- **Spring核心容器**
  - 容器是Spring框架最核心的部分,管理着Spring应用中bean的创建、配置和管理；该模块中，包括了Spring bean工厂，它为Spring提供了DI的功能。基于bean工厂，还会发现有多种Spring应用上下文的实现，每一种都提供了配置Spring的不同方式。该模块也提供了许多企业服务，例如E-mail、JNDI访问、EJB集成和调度。所有的Spring模块都构建于核心容器之上。配置应用时，会隐式地使用这些类。

- **Spring的AOP模块**
  - 在AOP模块中，Spring对面向切面编程提供了丰富的支持。这个模块是Spring应用系统中开发切面的基础。与DI一样，AOP可以帮助应用对象**解耦**。**借助于AOP，可将遍布系统的关注点(例如事务和安全)从它们所应用的对象中解耦出来。**
- **数据访问与集成**
  - Spring的JDBC和DAO(Data Access Object)模块抽象了样板式代码，使数据库代码变得简单明了，还可避免因关闭数据库资源失败而引发地问题。提供ORM模块，集成许多流行的ORM框架(Hibernate、Java Persisternce API、Java Data Object)；另外,本模块使用Spring AOP为Spring应用中的对象提供事务管理服务。

- **Web与远程调用**
  - 自带强大的MVC框架，有助于在web层提升应用的松耦合水平；除面向用户的Web应用，还提供多种构建与其他应用交互的远程调用方案。Spring远程调用功能集成了RMI(Remote Method Invocation)、Hessian、Burlap、JAX-WS，同时Spring自带远程调用框架：HTTP invoker,还提供暴露和使用REST API支持。

- **测试**
  - Spring提供测试模块；为使用JNDI、Servlet和Portlet编写单元测试提供一系列mock对象实现。对应用的集成测试也提供支持。

### 第2章   装配Bean

> **装配**---创建应用对象之间协作关系的行为，这也是**依赖注入(DI)的本质**。

#### 2.1   Spring配置的可选方案

> 在XML中进行显式配置；
>
> 在Java中进行显式配置；
>
> 隐式的bean发现机制和自动装配。

#### 2.2   自动化装配bean

Spring从两个角度来实现自动化装配:

> ==组件扫描(component scanning):Spring会自动发现应用上下文中所创建的bean。==
>
> ==自动装配(autowiring):Spring自动满足bean之间的依赖。==

- **组件扫描:**

```java
package soundsystem;
@Component //表明该类(SgtPeppers)会作为组件类，告知Spring要为该类创建bean(默认beanid为类名首字母              小写`setPeppers`);(需要启用组件扫描)自定义beanid---@Component("自定义beanid");另              一种bean命名方式,@Named("beanid")等同于@Component("beanid")
public class SgtPeppers implements CompactDisc{   //具体的SgtPeppers实现CD播放器接口
    
    private String title="Sgt. Pepper's Lonely Hearts Club Band";
    private String artist="The Beatles";
    
    public void play(){
        System.out.println("Playing"+title+"by"+artist);
    }
}

package soundsystem;
@Configuration
@ComponentScan//在Spring中启用组件扫描;默认扫描与配置类相同的包，扫描该包(soundsystem)以及其下的                 所有子包,查找带有@Component注解的类,这样就会发现CompactDisc，并在Spring中自动为                 其创建一个bean。
public class CDPlayerConfig{
}
@ComponentScan("包名")扫描指定包(基础包)及其下所有子包,查找带有@Component注解的类,在Spring容器中                      自动创建该类bean。
@ComponentScan(basePackages="包名")指定基础包(包名)
@ComponentScan(basePackages={"包1","包2"})指定扫描多个基础包(包),包名为String类型可以(这种方                              法类型不安全),但重构代码时可能出错。
另一种方式:将其指定为包中所包含的类或接口：
@ComponentScan(basePackageClasses={CDPlayer.class,DVDPlayer.class})
表示CDplayer和DVCPlaayer类所在的包会作为组件扫描的基础包。

//测试
@RunWith(SpringJUnit4ClassRunner.class)//在测试开始的时候自动创建Spring应用上下文.
@ContextConfiguration(classes=CDPlayerConfig.class)//表示上下文要在CDPlayerConfig中加载配置
public class CDPlayerTest{
    
    @Autowired//将CompactDisc的bean(实例)自动装配到CDPlayerTest类中
    private CompactDisc cd;
    
    @Test
    public void cdShouldNotBeNull(){
        assertNotNull(cd);
    }
}
```

- **自动装配**(在组件扫描完成的前提下)

```java
//通过自动装配,将一个CompactDisc注入到CDPlayer之中
package soundsystem;

@Component
public class CDPlayer implements MediaPlayer{
    private CompactDisc cd;
    
    @Autowired//用在构造器上
    public CDPlayer(CompactDisc cd){
        this.cd=cd;
    }
    
    public void play(){
        cd.play();
    }
    
    @Autowired
    public void setCompactDisc(CompactDisc cd){
        this.cd=cd;
    }
    //@Autowired可用在类的任何方法上,用来尝试满足方法参数上所声明的依赖(所需要的bean);
    //如果容器中没有匹配的bean,Spring会抛出异常，可用@Autowired(required=false)避免异常;这时如果       没有匹配的bean,Spring会让这个bean处于未装配的状态,但注意此时代码中可能出现               	   NullPointerException.
    
    //大多数情况下@Inject可替代@Autowired
}
```

#### 2.3   显式装配Bean

> 虽然尽可能地使用组件扫描和自动装配实现Spring自动化配置,但有时自动化配置方案行不通,需要明确配置。比如:要将第三方库中地组件装配到应用中,这种情况下没有办法在它的类上添加@Component,@Autowired这种情况下就要采用显式装配。
>
> **显式配置**:
>
> ==Java(JavaConfig)==----更好的选择相对于XML
>
> ==XML装配==

- **JavaConfig**
  - **带有@Bean注解的方法可采用任何必要的Java功能来产生Bean实例**

```java
package soundsystem;

@Configuration//标注于类上方,表明该类为一个配置类
public class CDPlayerConfig{
    
    @Bean//告诉Spring容器该方法会返回一个对象,该对象要注册为Spring应用上下文中的bean.
         //默认情况:bean的ID与带有@Bean注解的方法名是一样的.@Bean(name="自定义beanid")
    public CompactDisc sgtPeppers(){
        return new SgtPeppers();
    }
    
    @Bean
    public CDPlayer cdPlayer(){
        return new CDPlayer(sgtPeppers());//此处看似方法调用,实际不是@Bean会屏蔽方法调用,确保                                             其直接返回bean实例.
    }
    
    @Bean
    public CDPlayer anotherCDPlayer(){
        return new CDPlayer(sgtPeppers());
    }
    //可以将同一个SgtPeppers的bean实例注入到任意数量的其他bean当中。默认情况下,Spring中的bean都是单例的，因此以上两个CDPlayer-bean会得到相同的SgtPeppers-bean实例.
    //另一种便于理解的方式
    @Bean
    public CDPlayer cdPlayer(CompactDisc compactDisc){
        return new CDPlayer(compactDisc);
    }
    //这种方式为最佳选择;不要求将CompactDisc的bean声明在同一个配置类中,甚至它(CompactDisc的bean)可通过组件扫描功能自动发现或通过XML来配置；这样可以将配置分散到多个配置类、XML文件以及自动扫描和装配的bean之中,只要功能完整健全即可。(不管CompactDisc的bean是何种方式创建,Spring都会将其传入到配置方法中,并用来创建CDPlayer bean)
    
    //通过Setter方法注入CompactDisc
    @Bean
    public CDPlayer cdPlayer(CompactDisc compactDisc){
        CDPlayer cdPlayer=new CDPlayer(compactDisc);
        cdPlayer.setCompactDisc(compactDisc);
        return cdPlayer;
    }
}
```

- **XML装配**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" ---根命名空间
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans 
       http://www.springframework.org/schema/beans/spring-beans.xsd 
       http://www.springframework.org/schema/mvc 
       http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <bean id="compactDisc" class="soundsystem.SgtPeppers"/>
    <!--
		若不明确指定id属性及值,默认id是"soundsystem.SgtPeppers(全限定类名)#0"
        "#0"是一个计数形式,用来区分同类型其他bean。如另外一个默认id会是soundsystem.SgtPeppers#1.
		减少繁琐:为减少XML中繁琐配置，只对那些需要按名字引用的bean(比如，你需要将对它的引用注入到另外         一个bean中)进行明确命名。
    -->
    <bean id="cdPlayer" class="soundsystem.CDPlayer">
        <!--构造器注入初始化bean-->
        <constructor-arg ref="compactDisc" />
	</bean>
</beans>---根元素

<!--使用Spring的c命名空间:-->

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" ---根命名空间
       xmlns:c="http://www.springframework.org/schema/c"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans 
       http://www.springframework.org/schema/beans/spring-beans.xsd 
       http://www.springframework.org/schema/mvc 
       http://www.springframework.org/schema/mvc/spring-mvc.xsd">
       
    <!--使用c-命名空间来声明构造器参数-->
    <bean id="cdPlayer" class="soundsystem.CDPlayer" c:cd-ref="compactDisc"/>
    <!--替代方案参数保持相同顺序,(多个构造器参数很有用)-->
    <bean id="cdPlayer" class="soundsystem.CDPlayer" c:_0-ref="compactDisc"/>
    <!--只有一个构造器参数,另一个方案-->
    <bean id="cdPlayer" class="soundsystem.CDPlayer"c:_-ref="compactDisc"/>
    
    <!--将字面量注入到构造器中-->
    <bean id="compactDisc" class="soundsystem.BlankDisc">
        <constructor-arg value="Sgt. Pepper's Lonely Hearts Club Band"/>
        <constructor-arg value="The Beatles"/>
    </bean>
    <!--使用c-命名空间-->
    <bean id="compactDisc" class="soundsystem.BlankDisc"
          c:_title="Sgt. Pepper's Lonely Hearts Club Band"
          c:_artist="The Beatles"/>
    <!--使用c-命名空间和参数索引-->
    <bean id="compactDisc" class="soundsystem.BlankDisc"
          c:_0="Sgt. Pepper's Lonely Hearts Club Band"
          c:_1="The Beatles"/>
    <!--只有一个构造器参数-->
    <bean id="compactDisc" class="soundsystem.BlankDisc"
          c:_="Sgt. Pepper's Lonely Hearts Club Band"/>
</beans>---根元素
```

当实体类中有集合属性时:

```java
public class BlankDisc implements CompactDisc{
    private String title;
    private String artist;
    private List<String> tracks;
    
    public BlankDisc(String title,String artist,List<String> tracks){
        this.title=title;
        this.artist=artist;
        this.tracks=tracks;
    }
}
```

对应的xml配置文件(构造器注入初始化)如下:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" ---根命名空间
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans 
       http://www.springframework.org/schema/beans/spring-beans.xsd 
       http://www.springframework.org/schema/mvc 
       http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <bean id="compactDisc" class="soundsystem.BlankDisc">
        <constructor-arg value="Sgt. Pepper's Lonely Hearts Club Band"/>
        <constructor-arg value="The Beatles"/>
        <constructor-arg>
        	<list>
                <value>Sgt. Pepper's Lonely Hearts Club Band</value>
                <value>With a Little Help from My Friends</value>
                <value>Lucy in the Sky with Diamonds</value>
                <value>Getting Better</value>
                <value>Fixing a Hole</value>
                <!--...other tracks omitted for brevity-->
            </list>
        </constructor-arg>
    </bean>
    
    用<ref>元素替代<value>
    对应于这样一个Discography类:public Discography(String artist,List<CompactDisc> cds){}
    <!--采用如下配置-->
    <bean id="beatlesDiscography" class="soundsystem.Discography">
    	<constructor-arg value="The Beatles"/>
        <constructor-arg>
        	<list>
            	<ref bean="sgtPeppers"/>
                <ref bean="whiteAlbum"/>
                <ref bean="hardDaysNight"/>
                <ref bean="revolver"/>
                ...
            </list><!--set就用<set></set>-->
        </constructor-arg>
    </bean>
    <!---------------------------------------------------------------------------->
    对应于java类 CDPlayer{
    	private CompactDisc compactDisc;
    	
    public void play(){
    	compactDisc.play();
    }
    }
    <bean id="compactDisc" class="soundsystem.SgtPeppers"/>
    <!--设置属性-->
    <bean id="cdPlayer" class="soundsystem.CDPlayer">
        <property name="compactDisc" ref="compactDisc"/><!--property通过              		setCompactDisc()方法-->
    </bean>
    <!--使用p标签-->
    引入:<xmlns:p="http://www.springframework.org/schema/p>"
    <bean id="cdPlayer" class="soundsystem.CDPlayer" p:compactDisc(属性名）-         		ref="compactDisc"(注入的beanId)/>
    <!--属性中注入字面量-->
    <property name="" value=""/>
    <property name=""(list集合属性名)>
    	<list>
        	<value>...</value>
            <value>...</value>
            <value>...</value>
            <!--..........-->
        </list>
    </property>
    <!--使用p标签-->
    普通字面量属性: p:""="",其它与
    <property>
     <list>
         <value></value>
     </list>
    </property>一致
</beans>---根元素
```

#### 2.4   导入和混合配置

```java
@Configuration
public class CDConfig{
    @Bean
    public CompactDisc compactDisc(){
        return new SgtPeppers();
    }
}
//将两个JavaConfig组合起来
@Configuration
@Import(CDConfig.class)
public class CDPlayerConfig{
    
    @Bean
    public CDPlayer cdPlayer(CompactDisc compactDisc){
        return new CDPlayer(compactDisc);
    }
}
//或者创建一个更高级别的SoundSystemConfig类
@Configuration
@Import({CDPlayerConfig.class,CDConfig.class})
public class SoundSystemConfig{
}

//让Spring同时加载xml配置和其它基于Java的配置---使用@ImportResource注解
@Configuration
@Import(CDPlayerConfig.class)
@ImportResource("classpath:cd-config.xml")
public class SoundSystemConfig{
}
xml中组合配置使用
<import resource="xxx.xml"/>标签
xml中引入JavaConfig,这样配置bean
<bean id="" class="配置类全限类名(soundsystem.CDConfig)" c:cd-ref="compactDisc(beanId)"/>
//更高层次配置(xml)文件
<?xml version="1.0" encoding="UTF-8"?>
<beans>
	<bean class="soundsystem.CDConfig"/>
	<import resource="cdplayer-config.xml"/>
</beans>
```

==尽可能使用自动化配置,避免显示配置带来的维护成本。==

### 第3章 高级装配

#### 3.1 环境与profile

**针对于不同的环境(dev开发(集成测试)、pro生产等环境),同一类型的bean的具体产生有所不同,需要一种方法使同类型bean的生成在每种环境下有最合适的选择**；Spring提供如下解决方案(不需要重新构建项目)：

**bean profile**功能---将所有不同的bean定义整理到一个或多个profile之中，应用部署到环境时,激活(active)对应的profile生成对应的bean.

##### 3.1.1   配置profile bean

- **在JavaConfig中配置**

```java
package com.yuchen.spring.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Profile;

/**
 * @author
 */
@Configuration
public class TestConfig {

    @Bean
    @Profile("dev")
    public TestConfig config1(){
        return new TestConfig();
    }

    @Bean
    @Profile("prod")
    public TestConfig config2(){
        return new TestConfig();
    }
}
//测试
@RunWith(SpringRunner.class)
@SpringBootTest
@ContextConfiguration(classes = {TestConfig.class})//以TestConfig配置类声明的配置加载应用上下文
@ActiveProfiles("prod")//表示测试所处环境为:prod
public class SpringInactionApplicationTests {

    @Autowired
    ApplicationContext context;


    @Test
    public void contextLoads() {
        System.out.println(context.containsBean("config1"));//false
        System.out.println(context.containsBean("config2"));//true
    }

}
```

没有指定profile的bean始终都会被创建,与激活哪个profile没有关系。

- **在XML中配置profile**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd"
       profile="dev"><!--这里指定为开发环境(dev)-->

        <bean>
            <!--bean都是为dev环境下的-->
        </bean>
</beans>

<!--将所有的profile bean定义放到同一个XML文件中-->
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <beans profile="dev"><!--dev profile的bean-->
        <!--...-->
    </beans>
    
    <beans profile="prod"><!--prod profile的bean-->
        <!--...-->
    </beans>
</beans>
```

##### 3.1.2   激活profile

Spring确定profile处于激活状态,取决于`spring.profiles.active`和`spring.profiles.default`的值；

设置`spring.profiles.active=""`；未设置就取决于`spring.profiles.default=""`的值。如果这两个都没有设置,则只会创建那些没有定义profile的bean；

多种方式设置这两个属性 :

- 作为DispatcherServlet的初始化参数；

- 作为Web应用的上下文参数；

- 作为JNDI条目；
- 作为环境变量；
- 作为JVM的系统属性；
- 在集成测试类上，使用@ActiveProfiles注解设置；

```xml
<!--作为DispatcherServlet的初始化参数-->
<context-param>  <!--为上下文设置默认的profile-->
	<param-name>spring.profiles.default</param-name>
    <param-value>dev</param-value>
</context-param>
<servlet>
    <servlet-name> </servlet-name>
    <servlet-class>
      org.springframework.web.servlet.DispatcherServlet
    </servlet-class>
    <init-param>
    	<param-name>spring.profiles.default</param-name><!--为Servlet设置默认的profile-->
    	<param-value>dev</param-value>
    </init-param>
</servlet>
```

`spring.profiles.active=xxx,xxx`和`spring.profiles.default=xxx,xxx`也可同时激活多个profile;

#### 3.2   条件化的bean

```java
@Bean
@Conditional(MagicExistsCondition.class)//条件化创建bean---指明条件MagicExistsCondition
public MagicBean magicBean(){
    return new MagicBean();
}
@Conditional将会通过Condition接口进行条件对比：
public interface Condition{
	boolean matches(ConditionContext ctxt,AnnotatedTypeMetadata metadata);
}
设置给@Conditional的类可为任意实现Condition接口的类；如果matches()返回为true,则创建带有@Conditional注解的bean,否则就不创建；

//根据环境中是否存在magic属性
public class MagicExistsCondition implements Condition{
	public boolean matches(ConditionContext context,AnnotatedTypeMetadata metadata){
		Environment env=context.getEnvironment();
		return env.containsProperty("magic");
	}
}
//ConditionContext接口
public interface ConditionContext {
    BeanDefinitionRegistry getRegistry();//检查bean定义；

    @Nullable
    ConfigurableListableBeanFactory getBeanFactory();//检查bean是否存在,甚至探查bean属性；

    Environment getEnvironment();//检查环境变量是否存在以及它的值是什么；

    ResourceLoader getResourceLoader();//读取并探查ResourceLoader所加载的资源；

    @Nullable
    ClassLoader getClassLoader();//借助返回的ClassLoader加载并检查类是否存在；
}
//AnnotatedTypeMetadata能让我们检查带有@Bean注解的方法上还有什么其他的注解；
public interface AnnotatedTypeMetadata {
    boolean isAnnotated(String var1);//判断带有@Bean注解的方法是不是还有其他特定的注解；

//以下为检查@Bean注解的方法上其他注解的属性
    @Nullable
    Map<String, Object> getAnnotationAttributes(String var1);
	@Nullable
    Map<String, Object> getAnnotationAttributes(String var1, boolean var2);
	@Nullable
    MultiValueMap<String, Object> getAllAnnotationAttributes(String var1);
	@Nullable
    MultiValueMap<String, Object> getAllAnnotationAttributes(String var1, boolean var2);
}

//@Profile注解也使用了@Conditional注解
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Conditional({ProfileCondition.class})//ProfileCondition同样实现了Condition接口,重写                                               matches方法,用来检查profile是否处于激活状态
public @interface Profile {
    String[] value();
}
```

#### 3.3   处理自动装配的歧义性

仅有一个bean匹配,自动装配才是有效的。

```java
@Autowired
public void setDessert(Dessert dessert){
    this.dessert=dessert;
}

@Component
public class Cake implements Dessert{...}
@Component
public class Cookies implements Dessert{...}
@Component
public class IceCream implements Dessert{...}
//此时Spring自动装配会抛出 NoUniqueBeanDefinitionException异常
```

**Spring提供以下解决方案:**

##### 3.3.1   标示首选的bean

> 遇到歧义性时,Spring将会使用首选(**@Primary**)的bean

```java
//@Primary与@Component组合
@Component
@Primary//告诉Spring在遇到歧义性的时候要选择首选的bean
public class IceCream implements Dessert{...}
//用在java配置的bean声明中
@Bean
@Primary//告诉Spring在遇到歧义性的时候要选择首选的bean
public Dessert iceCream(){
    return new IceCream();
}
```

##### 3.3.2   限定自动装配的bean

> **Spring的限定符能够在所有可选的bean上进行缩小范围的操作，最终只有一个bean满足所规定的限制条件**
>
> **@Qualifier("")**注解是使用限定符的主要方式

```java
@Autowired
@Qualifier("iceCream")//限定beanId为iceCream
public void setDessert(Dessert dessert){
    this.dessert=dessert;
}
```

@Qualifier("iceCream")所引用的bean要具有String类型的"iceCream"作为限定符;bean默认的限定符与beanID相同；但如果重构了IceCream类，将其重命名的话，beanID和默认限定符都会改变,无法匹配自动装配就失败;

- 创建自定义的限定符

```java
//在bean声明上添加@Qualifier注解
@Component
@Qualifier("cold")//cold限定符分配给了IceCream bean,与类名解耦；因此可以随意重构IceCream类名
public class IceCream implements Dessert{...}

//或者当通过Java配置显式定义bean时,@Qualifier也可以与@Bean注解一起使用
@Bean
@Qualifier("cold")
public Dessert iceCream(){
    return new IceCream();
}
//在注入的地方,引用cold限定符
@Autowired
@Qualifier("cold")
public void setDessert(Dessert dessert){
    this.dessert=dessert;
}
```

- 使用自定义的限定符注解

> **当多个bean具备相同特性,即自定义的限定符相同时发生歧义；------创建自定义限定符注解**

```java
//自定义@Cold注解
@Target({ElementType.CONSTRUCTOR,ElementType.FIELD,ElementType.METHOD,ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Qualifier//定义时添加@Qualifier注解，就具有了其特性；本身实际就成为了限定符注解；
public @interface Cold {
}
//自定义@Creamy注解来取代@Qualifier("creamy")
@Target({ElementType.CONSTRUCTOR,ElementType.FIELD,ElementType.METHOD,ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Qualifier
public @interface Creamy {
}
//IceCream添加自定义注解@Cold,@Creamy
@Component
@Cold
@Creamy
public class IceCream implements Dessert{...}
//Popsicle类添加@Cold和@Fruity注解
@Component
@Cold
@Fruity
public class Popsicle implements Dessert{...}

//注入时使用必要的限定符注解进行任意组合，例如为得到IceCream bean
@Autowired
@Cold
@Creamy
public void setDessert(Dessert dessert){ //匹配IceCream bean
    this.dessert=dessert;
}
```

#### 3.4   bean的作用域

> **默认情况下，Spring应用上下文中所有bean都是作为以单例(singleton)的形式创建的；**

**Spring定义了多种作用域，可以基于这些作用域创建bean：**

- 单例(Singleton)：在整个应用中，只创建bean的一个实例；
- 原型(Prototype)：每次注入或者通过Spring应用上下文获取的时候，都会创建一个新的bean实例；
- 会话(Session)：在Web应用中，为每个会话创建一个bean实例；
- 请求(Request)：在Web应用中，为每个请求创建一个bean实例；

单例是默认的作用域,但对于易变的类型并不适合；如果选择其他的作用域，使用@Scope注解

```java
@Component
@Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)//更加安全不出错,也可使用@Scope("prototype")
public class Notepad{...}
//Java配置类中
@Bean
@Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)//声明为原型bean 
public Notepad notepad(){  
    return new Notepad();
}
//XML配置bean
<bean id="notepad"
      class="com.myapp.Notepad"
      scope="prototype"/>
```

##### 3.4.1使用会话和请求作用域

在Web应用中，实例化在会话和请求范围内共享的bean是非常有价值的；

假设在电商应用中,有一个bean代表用户的购物车；如果购物车是单例，那么所有用户添加商品到同一个购物车；如果购物车是原型作用域的，那么在应用中某一个地方往购物车添加商品，在另一个地方就不可用了；

就购物车bean来说，会话作用域是最合适的，它与给定用户关联性最大；

```java
//指定会话作用域
@Component
@Scope(
        value= WebApplicationContext.SCOPE_SESSION,//session作用域,为每个会话创建bean实例
        proxyMode = ScopedProxyMode.INTERFACES     //在一个会话内bean相当于是单例的;
)
public ShoppingCart cart(){...}
```

**proxyMode**所解决问题的场景(如下):

```java
@Component
public class StoreService{
    
    @Autowired
    public void setShoppingCart(ShoppingCart shoppingCart){
     	this.shoppingCart=shoppingCart;   
    }
}
```

StoreService默认是一个单例的bean,在应用上下文加载的时候创建；当它创建时，Spring会试图将ShoppingCart **bean**注入到setShoppingCart()方法中；但ShoppingCart  **bean**是会话作用域的，此时并不存在；直到某个用户进入系统，创建会话后才出现ShoppingCart实例。

另外，系统中将会有多个ShoppingCart实例：每个用户一个。我们不想让Spring注入某个固定的ShoppingCart实例到StoreService;希望的是当StoreService处理购物车功能时，它所使用的ShoppingCart实例恰好是当前会话对应的那一个。

**Spring不会注入实际的ShoppingCart bean，而是注入一个ShoppingCart bean的代理**，如下图：

![作用域代理](E:\Java进阶(note)\Spring实战(第4版)\images\作用域代理.jpg)

这个代理会暴露与ShoppingCart相同的方法，所以StoreService会认为它就是一个购物车；但是，当StoreService调用ShoppingCart的方法时，**代理会对其进行懒解析并将调用委托给会话作用域内真正的ShoppingCart bean**。

**proxyMode = ScopedProxyMode.INTERFACES**==表明这个代理要实现ShoppingCart接口，并将调用委托给实现bean。==

如果ShoppingCart是接口不是类的话，这是可以的(最为理想的代理模式)；ShoppingCart是一个具体的类的话，Spring就没有办法创建基于接口的代理了；此时**必须使用CGLIB来生成基于类的代理，proxyMode = ScopedProxyMode.TARGET_CLASS**，以此来表明要以生成目标类扩展的方式创建代理；

##### 3.4.2XML中声明作用域代理

```xml
<bean id="cart"
      class="com.myapp.ShoppingCart"
      scope="session">
<aop:scoped-proxy proxy-target-class="false"/>//生成基于接口的代理
</bean>

<aop:scoped-proxy>是与@Scope注解的proxyMode属性功能相同的Spring XML配置元素；默认情况下使用CGLib创建目标类的代理；
```

#### 3.5   运行时值注入

**Spring提供两种在运行时求值的方式**：

> - 属性占位符(Property placeholder)
> - Spring表达式语言(SpEL)

##### 3.5.1   注入外部的值

```java
@Configuration
@PropertySource("classpath:app.properties")//声明属性源
public class ExpressiveConfig {

    @Autowired
    Environment env;//通过Spring的Environment来检索属性

    @Bean
    public BlankDisc disc() {
        return new BlankDisc(
                env.getProperty("disc.title"),//与app.properties文件中disc.title值绑定
                env.getProperty("disc.artist")//与app.properties文件中disc.artist值绑定
        );
    }
}

//BlankDisc类
public class BlankDisc {

    private String title;
    private String artist;

    public BlankDisc(String title, String artist) {
        this.title = title;
        this.artist = artist;
    }

    @Override
    public String toString() {
        return "BlankDisc{" +
                "title='" + title + '\'' +
                ", artist='" + artist + '\'' +
                '}';
    }

}
```

app.properties的内容：

```properties
disc.title=aaa
disc.artist=bbb
```

- **Spring的Environment**:

```java
public interface Environment extends PropertyResolver {
    String[] getActiveProfiles();//返回激活profile名称的数组

    String[] getDefaultProfiles();//返回默认profile名称的数组

    /** @deprecated */
    @Deprecated
    boolean acceptsProfiles(String... var1);//如果支持给定profile返回为true

    boolean acceptsProfiles(Profiles var1);
}
//PropertyResolver
public interface PropertyResolver {
    boolean containsProperty(String var1);//检查某个属性是否存在

    @Nullable
    String getProperty(String var1);

    String getProperty(String var1, String var2);//var1值不存在，默认设使用值为var2;

    @Nullable
    <T> T getProperty(String var1, Class<T> var2);

    <T> T getProperty(String var1, Class<T> var2, T var3);//可将获得的String类型值指定转换成Class<T>

    String getRequiredProperty(String var1) throws IllegalStateException;//属性var1必须设定否则会抛IllegalStateException异常

    <T> T getRequiredProperty(String var1, Class<T> var2) throws IllegalStateException;

    String resolvePlaceholders(String var1);

    String resolveRequiredPlaceholders(String var1) throws IllegalArgumentException;
}
```

- **解析属性占位符**

Spring中,占位符形式使用为"${...}",可使用占位符将设定值插入bean属性中;

xml形式:

```xml
<bean id="sgtPeppers"
      class="soundsystem.BlankDisc"
      c:_title="${disc.title}"//可从配置文件以外的一个源中解析得到
      c:_artist="${disc.artist}"/>
```

JavaConfig形式:

```java
public BlankDisc(
	@Value("${disc.title}") String title,
    @Value("${disc.artist}") String artist){
    this.title=title;
    this.artist=artist;
}
```

为使用占位符，必须配置PropertySourcesPlaceholderConfigurer,如下：

```java
@Bean
public static PropertySourcesPlaceholderConfigurer placeholderConfigurer(){
    return new  PropertySourcesPlaceholderConfigurer();
}
```

##### 3.5.2   使用Spring表达式语言进行装配

**Spring表达式语言(Spring Expression Language，SpEL)**,**SpEL拥有很多特性**：

> - **使用bean的ID来引用bean；**
>
> - **调用方法和访问对象的属性；**
>
> - **对值进行算术、关系和逻辑运算；**
> - **正则表达式匹配；**
> - **集合操作。**

SpEL表达式(#{...})样例:

#{T(System).currentTimeMills}---计算当前时间毫秒数；

#{sgtPeppers.artist}---得到ID为sgtPeppers的bean的artist属性；

#{systemProperties['disc.title']}---引用系统属性

```java
public BlankDisc(
	@Value("#{systemProperties['disc.title']}") String title,
    @Value("#{systemProperties['disc.artist']}") String artist){
    this.title=title;
    this.artist=artist;
}
```

```xml
<bean id="sgtPeppers"
      class="soundsystem.BlankDisc"
      c:_title="#{systemProperties['disc.title']}"//可从配置文件以外的一个源中解析得到
      c:_artist="#{systemProperties['disc.artist']}"/>
```

表示字面值：

#{3.14159}    #{9.87E4}  #{'Hello'}  #{false}

引用bean、属性和方法---通过bean的ID引用其他的bean

#{sgtPeppers}

#{sgtPeppers.artist}---引用ID为sgtPeppers的bean的artist属性；

#{artistSelector.selectArtist()}---调用ID为artistSelector的bean的selectArtist()方法；

#{artistSelector.selectArtist().toUpperCase}---调用返回为String类型的toUpperCase()方法；

#{artistSelector.selectArtist()?.toUpperCase}---"?.运算符"调用toUpperCase()方法前判断前面对象是否为null,是null则表达式直接返回为null；

#{'This is a test'[3]}---引用String中的第四个(基于零)字符，也就是"s"；

#{disc.title?  :  'Rattle and Hum'}---该表达式先判断disc.title的值是否为null，如果是null,计算结果就会是"Rattle and Hum"；

### 第4章  面向切面的Spring

#### 	4.1  什么是面向切面编程

​		软件开发中，**散布于应用中的多处功能被称为**-**横切关注点**(如日志、安全、事务)(==概念上与应用的业务   逻辑相分离==);

![切面](E:\Java进阶(note)\Spring实战(第4版)\images\切面.jpg)

**切面**---模块化横切关注点;

**使用面向切面编程**---在一个地方定义通用功能，可通过声明定义该功能以何种方式在何处应用，无需修改受影响的类；**横切关注点可被模块化为特殊的类--切面**。

##### 	4.1.1 AOP术语

![AOP](E:\Java进阶(note)\Spring实战(第4版)\images\AOP.jpg)

**通知(Advice)-**--定义了切面是什么以及何时使用；

Spring切面可以应用5种类型通知：

- 前置通知(Before):目标方法调用前调用通知；
- 后置通知(After):目标方法完成后调用通知;
- 返回通知(After-returning):目标方法成功执行之后调用通知；
- 异常通知(After-throwing):目标方法抛出异常后调用通知；
- 环绕通知(Around):通知包裹了被通知的方法，在被通知的方法调用之前和调用之后执行自定义的行为。



**连接点(Join Point)**---应用执行过程种能够插入切面的一个点。

**切点(Poincut)**---具体的要匹配织入的一个或多个连接点(**哪些连接点会得到通知**)；

**切面(Aspect)**---**通知**和**切点**的结合(定义了切面是什么，在何时何处使用);

**引入(Introduction)**---在无需修改现有类的情况下，添加新方法和属性，让现有类具有新的行为和状态；

**织入(Weaving)**---把切面应用到目标对象并创建新的代理对象的过程；

##### 	4.1.2  Spring对AOP的支持

- 基于代理的经典Spring AOP;
- 纯POJO切面;
- @AspectJ注解驱动的切面；
- 注入式AspectJ切面(适用于Spring各版本);

**Spring通知是JAVA编写,且Spring AOP是运行时通知对象，采用动态代理，只支持方法连接点--即在目标对象的方法执行前后，织入切面执行切面逻辑；**

![Spring切面](E:\Java进阶(note)\Spring实战(第4版)\images\Spring切面.jpg)

#### 4.2  通过切点来选择连接点

切面的最基本元素---**通知和切点(准确定位应该在什么地方应用切面的通知)**；

Spring借助**AspectJ的切点表达式**语言来定义Spring切面：

- 编写切点

```java
package com.yuchen.spring.concert;
public interface Performance {
    public void perform();
}
AspectJ切点表达式，设置当perform()方法执行时触发通知的调用
execution(* com.yuchen.spring.concert.Performance.perform(..))
//execution-在方法执行时触发;*表示返回任意类型;全限定类名和方法名；方法参数列表(..)表明切点选择任意的perform()方法，无论方法的入参是什么。
execution(* com.yuchen.spring.concert.Performance.perform(..)
         && within(concert.*))  //把execution()和within()指示器连一起形成与(and)关系;切点必须匹配所有的指示器; within(concert.*))当concert包下的任意类的perform()方法被调用时;or-||,not-!;
//bean指示器
execution(* concert.Performance.perform()) and bean('woodstock')执行Performance的perform()方法时应用通知，但限定bean的ID为woodstock;
execution(* concert.Performance.perform()) and ! bean('woodstock')此时,切面的通知会被编织到所有ID不为woodstock的bean中。
```

#### 4.3  使用注解创建切面

```java
package com.yuchen.spring.concert;

import org.aspectj.lang.annotation.*;

@Aspect//声明Audience类是一个切面
public class Audience {

    //@After 通知方法会在目标方法返回或抛出异常后调用
    //@Around 通知方法会将目标方法封装起来

    @Pointcut("execution (**com.yuchen.spring.concert.Performance.perform(..))")//定义可重用的切点
    public void performance(){}//空方法，本身是一个标识，供@Pointcut注解依附;

    @Before("performance()")//通知方法会在目标方法调用之前执行
    public void silenceCellPhones(){
        System.out.println("Silencing cell phones");
    }

    @Before("performance()")
    public void takeSeats(){
        System.out.println("Taking seats");
    }

    @AfterReturning("performance()")//通知方法会在目标方法返回后调用
    public void applause(){
        System.out.println("CLAP CLAP CLAP");
    }

    @AfterThrowing("performance()")//通知方法会在目标方法抛出异常后调用
    public void demandRefund(){
        System.out.println("Demanding a refund");
    }
}
```

**解析@AspectJ注解，创建将其转换为切面的代理**：

```java
//使用JavaConfig
package com.yuchen.spring.config;

import com.yuchen.spring.concert.Audience;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.EnableAspectJAutoProxy;

/**
 * @author
 */
@Configuration
@EnableAspectJAutoProxy//启用AspectJ自动代理
@ComponentScan
public class ConcertConfig {

    @Bean
    public Audience audience(){
        return new Audience();
    }
}
//xml装配bean，使用Spring aop命名空间中的<aop:aspectj-autoproxy>元素
<aop:aspectj-autoproxy/> <!--启用AspectJ自动代理-->
<bean class="com.yuchen.spring.concert.Audience" /> <!--声明Audience bean-->
    
这样AspectJ自动代理都会为使用@Aspect注解的bean创建一个代理，这个代理会围绕着所有该切面的切点所匹配的bean; 这时会为Concert bean创建一个代理，Audience类中的通知方法将会在perform()调用前后执行；

Spring的AspectJ自动代理本质上依然是 Spring基于代理的切面；尽管使用的@AspectJ注解,但仍然限于代理方法的调用。
```

- ##### 环绕通知

```java
package com.yuchen.spring.concert;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Pointcut;

/**
 * @author
 */
@Aspect
public class Audiencex {

    @Pointcut("execution (**com.yuchen.spring.concert.Performance.perform(..))")
    public void performance(){}//空方法，本身是一个标识，供@Pointcut注解依附;

    //@Around 通知方法会将目标方法封装起来
    @Around("performance()")//环绕通知watchPerformance()方法会作为performance()切点的环绕通知;
    public void watchPerformance(ProceedingJoinPoint jp){
        try {
            System.out.println("Silencing cell phones");
            System.out.println("Taking seats");
            jp.proceed();//表明要将控制权交给被通知的方法,若不调用,会阻塞对被通知方法的访问。
            System.out.println("CLAP CLAP CLAP!!!");
        } catch (Throwable e) {
            System.out.println("Demanding a refund");
        }
    }
}

```

- 通知中含有参数---映射被通知方法的形参

```java
package com.yuchen.spring.concert;

import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;

import java.util.HashMap;
import java.util.Map;

/**
 * @author
 */
@Aspect
public class TrackCounter {

    private Map<Integer,Integer> trackCounts=new HashMap<Integer,Integer>();

    @Pointcut("execution(* com.yuchen.spring.concert.CompactDisc.playTrack(int))"
                +"&& args(tarckNumber)")//被通知的playTrack(int)方法接受int类型参数，args(trackNumber)指定切点方法即trackPlayed()方法的参数;表明传递给playTrack()方法的int类型参数也会传递到通知中去;参数的名称trackNumber也与切点方法签名中的参数相匹配；
    public void trackPlayed(int trackNumber){} //切点方法签名

    @Before("trackPlayed(trackNumber)")
    public void countTrack(int trackNumber){
        int currentCount=getPlayCount(trackNumber);
        trackCounts.put(trackNumber,currentCount+1);
    }

    public int getPlayCount(int trackNumber){
        return trackCounts.containsKey(trackNumber)?trackCounts.get(trackNumber):0;
    }
}
```

- 通过注解引入新功能

利用被称为引入的AOP概念，切面可以为Spring bean添加新方法；

![被引入的AOP](E:\Java进阶(note)\Spring实战(第4版)\images\被引入的AOP.jpg)

```java
package com.yuchen.spring.concert;

import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.DeclareParents;

/**
 * @author
 */
@Aspect
public class EncoreableIntroducer {
    
    /*
        @DeclareParents注解组成:
        value属性指定了哪种类型的bean要引入该接口;+号表示是Performance的所有子类型;
        defaultImpl属性指定了为引入功能提供实现的类;
        所标注的静态属性指明了要引入了接口，这里引入Encoreable接口;
     */
    @DeclareParents(value = "com.yuchen.spring.concert.Performance+",
                    defaultImpl = DefaultEncoreable.class)
    public static Encoreable encoreable;
}
之后将EncoreableIntroducer声明为一个bean;
```

#### 4.4  在XML中声明切面

| AOP配置元素               | 用途                                                         |
| ------------------------- | ------------------------------------------------------------ |
| <<aop:advisor>>           | 定义AOP通知器                                                |
| <<aop:after>>             | 定义AOP后置通知(不管被通知的方法是否执行)                    |
| <<aop:after-returning>>   | 定义AOP返回通知                                              |
| <<aop:after-throwing>>    | 定义AOP异常通知                                              |
| <<aop:around>>            | 定义AOP环绕通知                                              |
| <<aop:aspect>>            | 定义一个切面                                                 |
| <<aop:aspectj-autoproxy>> | 启用@AspectJ注解驱动的切面                                   |
| <<aop:before>>            | 定义一个AOP前置通知                                          |
| <<aop:config>>            | 顶层的AOP配置元素；大多数的<<aop:*>>元素必须包含在<<aop:config>>元素内 |
| <<aop:declare-parents>>   | 以透明的方式为被通知的对象引入额外的接口                     |
| <<aop:pointcut>>          | 定义一个切点                                                 |

