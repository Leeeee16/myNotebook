#### `SpringBoot`

> 简单的整理，有待详细学习后补全

##### `SpringBoot`如何自动装配

* `@SpringBootApplication`这个注解的作用

  主要是三个注解

  * `@SpringBootConfiguration`(实质就是一个`@Configuration`)：代表当前类是一个配置类
  * @ComponentScan：根据指定的配置，扫描`package`

  * **`@EnableAutoConfiguration`**

    ```java
    @AutoConfigurationPackage
    @Import(AutoConfigurationImportSelector.class)
    public @interface EnableAutoConfiguration {}
    ```

    1. @AutoConfigurationPackage

       自动配置包，指定了默认的包规则

       ```java
       @Import(AutoConfigurationPackages.Registrar.class)  //给容器中导入一个组件
       public @interface AutoConfigurationPackage {}
       
       //利用Registrar给容器中导入一系列组件
       //将指定的一个包下的所有组件导入进来？MainApplication 所在包下。
       ```

    2. @Import(AutoConfigurationImportSelector.class)

       ```java
       1、利用selectImports方法里的getAutoConfigurationEntry(annotationMetadata);给容器中批量导入一些组件
       2、getAutoConfigurationEntry方法中，调用List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes)获取到所有需要导入到容器中的候选配置类，127个组件
       3、利用工厂加载 Map<String, List<String>> loadSpringFactories(@Nullable ClassLoader classLoader)；获取资源文件，位置是META-INF，得到所有的组件
       4、从META-INF/spring.factories位置来加载一个文件。
           默认扫描我们当前系统里面所有META-INF/spring.factories位置的文件
           spring-boot-autoconfigure-2.3.4.RELEASE.jar包里面也有META-INF/spring.factories
       ```

    ![image.png](https://cdn.nlark.com/yuque/0/2020/png/1354552/1602845382065-5c41abf5-ee10-4c93-89e4-2a9b831c3ceb.png)

    文件里面写死了spring-boot一启动就要给容器中加载的所有配置类

    虽然我们127个场景的所有自动配置启动的时候默认全部加载。xxxxAutoConfiguration
    按照条件装配规则（@Conditional），最终会按需配置。

    

##### `Spring`注入依赖的方式、原理

1. 属性注入：通过set方法注入Bean的属性或依赖对象，比较灵活

   ```xml
   <bean id=".." class="com.xxx.xx.x">
   	<property name="aaa" value="bbb"/>
   </bean>
   ```

2. 构造函数注入

   1. 按类型匹配入参

      ```xml
      <!--1.根据参数类型注入-->
      <bean id="car1" class="com.vtstar.entity.Car">
          <constructor-arg type="int" value="300"/>
          <constructor-arg type="java.lang.String" value="红旗"/>
          <constructor-arg type="double" value="20000000.9"/>
      </bean>
      ```

   2. 按索引匹配入参：如果构造函数有相同类型的入参，第一种用不了

      ```xml
      <!--2.通过入参位置下标-->
      <bean id="car2" class="com.vtstar.entity.Car">
          <constructor-arg index="0" value="400"/>
          <constructor-arg index="1" value="大众辉腾"/>
          <constructor-arg index="2" value="20000000"/>
      </bean>
      ```

   3. 联合使用类型和索引匹配入参

      ```java
          public Car(String brand, String corp, double price) {
              System.out.println("brand :" + brand + " corp :" + corp + " price :"+price);
              this.brand = brand;
              this.corp = corp;
              this.price = price;
          }
          public Car(String brand, String corp, int maxSpeed) {
              System.out.println("brand :" + brand + " corp :" + corp + " maxSpeed :"+maxSpeed);
              this.brand = brand;
              this.corp = corp;
              this.maxSpeed = maxSpeed;
          }
      ```

      ```xml
      <!--3.通过参数类型和入参位置联合注入-->
      <bean id="car3" class="com.vtstar.entity.Car">
          <constructor-arg index="0" type="java.lang.String" value="30000000.0"/>
          <constructor-arg index="1" type="java.lang.String" value="卡迪拉克"/>
          <constructor-arg index="2" type="int" value="400"/>
      </bean>
      ```

      第三个属性位置相同但类型不同，需结合下标和类型

   4. 自身类型反射入参

      ```xml
      <!--4.通过自身类型反射入参-->
      <bean id="boss" class="com.vtstar.entity.Boss">
          <constructor-arg value="Tom"/>
          <constructor-arg ref="car1"/>
          <constructor-arg value="20"/>
      </bean>
      ```

      

   构造函数注入方式优点：

   1. 可以保证一些重要的属性在Bean实例化的时候就设置好，
   2. 不用setter方法
   3. 更好地封装

   缺点：

   1. 类属性太多，可读性降低
   2. 不灵活
   3. 多个构造函数复杂
   4. 不利于类的继承和拓展
   5. 可能会循环依赖

3. 工厂注入

   分为静态工厂和非静态工厂

   ```java
   public class CarFactory {
       
       public Car createHongQiCar(){
           Car car = new Car();
           car.setBrand("红旗H1");
           System.out.println("这里是非静态工厂的创建..." + car.getBrand());
           return car;
       }
   
       public static Car createDaZhongCar(){
           Car car = new Car();
           car.setBrand("大众GoGoGo");
           System.out.println("这里是静态工厂的创建..." + car.getBrand());
           return car;
       }
   }
   ```

   ```xml
   <!--非静态注入工厂方法-->
   <bean id="carFactory" class="com.vtstar.ioc.CarFactory"/>
   <bean id="car5" factory-bean="carFactory" factory-method="createHongQiCar"/>
   
   <!--静态注入工厂方法-->
   <bean id="car6" class="com.vtstar.ioc.CarFactory" factory-method="createDaZhongCar"/>
   ```

   

##### `Spring` `Bean`生命周期、`Spring`生命周期方法

![img](https://pic1.zhimg.com/80/v2-8787f1b3800b71f6234e996d432e0a0c_1440w.jpg?source=1940ef5c)

Spring启动，查找并加载需要被Spring管理的bean，进行Bean的实例化

Bean实例化后对将Bean的引入和值注入到Bean的属性中

如果Bean实现了BeanNameAware接口的话，Spring将Bean的Id传递给setBeanName()方法

如果Bean实现了BeanFactoryAware接口的话，Spring将调用setBeanFactory()方法，将BeanFactory容器实例传入

如果Bean实现了ApplicationContextAware接口的话，Spring将调用Bean的setApplicationContext()方法，将bean所在应用上下文引用传入进来

如果Bean实现了BeanPostProcessor接口，Spring就将调用他们的postProcessBeforeInitialization()方法。

如果Bean 实现了InitializingBean接口，Spring将调用他们的afterPropertiesSet()方法。类似的，如果bean使用init-method声明了初始化方法，该方法也会被调用

如果Bean 实现了BeanPostProcessor接口，Spring就将调用他们的postProcessAfterInitialization()方法。

此时，Bean已经准备就绪，可以被应用程序使用了。他们将一直驻留在应用上下文中，直到应用上下文被销毁。

如果bean实现了DisposableBean接口，Spring将调用它的destory()接口方法，同样，如果bean使用了destory-method 声明销毁方法，该方法也会被调用。

##### 

##### `Spring IOC`原理和缺点

要讲IOC，先解释**依赖倒置**原则。举一个汽车生产的例子。比如米其林生产轮胎，如果我们的汽车根据轮胎生产底盘，根据底盘生产车身，根据车身设计车子，那么如果轮胎发生变化，我们整个车子都要重新设计。他们的依赖关系是车子依赖车身，车身依赖底盘，底盘依赖轮子，即高层依赖低层。这样低层代码一改，上层都要跟着改。。。

~~我们将依赖倒置，即**低层依赖高层**。轮子依赖底盘，底盘依赖车身，车身依赖车子。代码层面将**低层的类**作为**参数**传递给上层。这样若要修改轮胎，就不会影响到上层。~~

**依赖倒置**：高层模块不依赖于低层模块，**两者都应该依赖抽象**，抽象不依赖于细节，细节应该依赖于抽象。这里可以理解为双方不互相依赖，而是依赖第三方。**解耦**

**控制反转(IOC)**就是**依赖倒置原则**的一种代码设计思路。具体采用的方法就是**依赖注入**(DI)。我们可以采用**构造函数**、**Setter**和**接口**的方式传入参数。

依赖注入：甲方开放接口，在需要的时候将乙方传递进来(注入)

控制反转：甲乙双方互不依赖，双方的交易活动互不依赖，而由第三方来管理

由上我们可以得知，初始化一个高层的类时，我们就需要写大量的new，为了解决这个问题，就有了**控制反转容器**(IOC 容器)。这个容器可以可以通过配置文件，自动对代码进行初始化。好处是不用自己从底层往上new了。IOC容器会从高层向下查依赖关系，到达底层后一步步向上new，类似DFS。

Spring的IOC，是将控制权(创建对象和对象之间的依赖关系的权利)交给Spring容器，将耦合代码放到XML文件中统一管理

IOC的优点：解耦，提高可维护性

IOC的缺点：通过反射创建对象，效率有损耗。





##### AOP原理

面向切面编程，将与业务无关，但是为业务模块所调用的逻辑封装起来。业务是核心关注点，其他关系不大的是横切关注点。横切关注点各处基本相似，如权限验证、日志、事务处理。

切面aspect：切点pointcut和advice处理

pointcut切点：在业务代码中的哪里进行增强

advice：做什么增强，在哪里增强。before、after、around、afterReturning、afterThrowing



一个接口想设置多个切面？如何管理执行顺序？通过@Order注解，数字越小，越先执行

相关注解：

**`@pointcut：`**用来定义一个切点

`execution(* com.mutest.controller..*.*(..)))`

第一个 * 号表示返回类型

包名：需要拦截的包名，两个 句点表示当前包和当前包的所有子包

第二个 * 号表示所有的类

*(..)： * 表示所有方法，括号两个点表示任何参数

**`@Around：`**可自由选择执行动作与目标方法的执行顺序，可以改变参数值。需要**线程安全**的情况下使用

**第一个形参必须是 `ProceedingJoinPoint` 类型**，调用`ProceedingJoinPoint`的`proceed`方法才会执行目标方法。还可以传入一个`Object[ ]`对象，该数组中的值将被传入目标方法作为实参——**这就是`Around`增强处理方法可以改变目标方法参数值的关键**

普通使用Before和AfterReturning可以解决，如果目标方法执行前后要共享某种状态数据，则需要使用Around

@Before和@After，目标方法执行后和目标方法执行前，可以做一些统计或者日志

@AfterReturning，可以捕获切入方法的返回值，对返回值进行业务逻辑上的处理

@AfterThrowing：被切方法抛出异常后，进入注解的方法中执行

##### Spring事务隔离级别(和数据库那块差不多)

并发事务引起的问题：

脏读：一个事务读取了另一个事务修改但未提交的数据。如果写操作回滚了，第一个事务读取的数据就无效了

不可重复读：一个事务执行相同的查询两次或两次以上，但是每次都得到不同的数据时。这通常是因为另一个并发事务在两次查询期间进行了**更新**。

幻读：它发生在一个事务（T1）**读取了几行数据**，接着另一个并发事务（T2）**插入**了一些数据时。在随后的查询中，第一个事务（T1）就会发现多了一些原本不存在的记录。

**隔离级别：**

ISOLATION_DEFAULT：使用后端数据库默认的隔离级别

ISOLATION_READ_UNCOMMITTED：最低的隔离级别，允许读取尚未提交的数据变更，可能会导致脏读、幻读或不可重复读

ISOLATION_READ_COMMITTED： 允许读取并发事务已经提交的数据，可以阻止脏读，但是幻读或不可重复读仍有可能发生

ISOLATION_REPEATABLE_READ：对同一字段的多次读取结果都是一致的，除非数据是被本身事务自己所修改，可以阻止脏读和不可重复读，但幻读仍有可能发生

ISOLATION_SERIALIZABLE：最高的隔离级别，完全服从ACID的隔离级别，确保阻止脏读、不可重复读以及幻读，也是最慢的事务隔离级别，因为它通常是通过完全锁定事务相关的数据库表来实现的



##### Spring事务传播机制/传播特性？

七种传播行为

PROPAGATION_REQUIRED：当前方法必须运行在事务中，如果当前事务存在，方法将会在该事务中运行。否则，会启动一个新的事务

PROPAGATION_REQUIRED_NEW：表示当前方法必须运行在它自己的事务中。一个新的事务将被启动。如果外层事务失败回滚了，内层事务的结果仍然会被提交

PROPAGATION_SUPPORTS：是否使用事务取决于调用方法是否有事务，如果有则直接用，如果没有则不使用事务

PROPAGATION_NOT_SUPPORTED：不为当前方法开启事务，该方法运行期间，当前事务被挂起

PROPAGATION_MANDATORY：必须在已有事务下被调用，否则抛出异常 `IllegalTransactionStateException`

PROPAGATION_NEVER：不应该运行在事务上下文中，如果当前有事务抛出异常

PROPAGATION_NESTED：当前已经存在一个事务，那么该方法将会在嵌套事务中运行。嵌套的事务可以独立于当前事务进行单独地提交或回滚。Nested事务回滚，不影响外部事务。只回滚自己内部执行的SQL，不回滚主方法的SQL

##### 工厂模式在Spring源码中的应用

##### Spring中的常用注解

1. @SpringBootApplication：main方法的注解

   三个注解：@SpringBootConfiguration、@EnableAutoConfiguration、@ComponentScan

2. 我们一般使用 `@Autowired` 注解让 Spring 容器帮我们自动装配 bean。要想把类标识成可用于 `@Autowired` 注解自动装配的 bean 的类,可以采用以下注解实现：

   - `@Component` ：通用的注解，可标注任意类为 `Spring` 组件。如果一个 Bean 不知道属于哪个层，可以使用`@Component` 注解标注。
   - `@Repository` : 对应持久层即 Dao 层，主要用于数据库相关操作。
   - `@Service` : 对应服务层，主要涉及一些复杂的逻辑，需要用到 Dao 层。
   - `@Controller` : 对应 Spring MVC 控制层，主要用户接受用户请求并调用 Service 层返回数据给前端页面。

3. @Repository：对应Dao层，用于数据库相关操作

4. @RestController：@Controller和@ResponseBody的集合，用于前后端分离，返回JSON或XML数据

5. @Configuration：配置类

6. @GetMapping("users")：Get请求，同@RequestMapping(value="/users",method=RequestMethod.GET)

7. @PostMapping("users")：POST请求，同@RequestMapping(value="/users",method=RequestMethod.POST)

8. @RequestBody：读取request请求的body部分

9. @Transactional：该方法开启事务，7个事务传播行为

字段验证注解

1. @NullEmpty：非null非空
2. @NullBlank：非null，必须包含一个非空白字符
3. @NotNull：必须不为null
4. @Min(Value)：必须是数字，且大于等于最小值
5. @Max(Value)：必须是数字，且小于等于最大值
6. Size(max, min)：范围内

##### Spring启动流程



##### Spring IOC容器启动流程(待补全)

从`ClassPathXMLApplicationContext`的构造函数说起，核心方法是**`refresh()`**，`refresh`是`synchronized`加锁的

* `prepareRefresh()`：记录启动时间、标记已启动状态、处理占位符、校验`xml`文件

* `obtainFreshBeanFactory()`：初始化BeanFactory、加载Bean、注册Bean，这步并没有初始化Bean

  * 调用`refreshBeanFactory()`

    判断当前ApplicationContext是否已经加载过BeanFactory，若有则销毁Bean，关闭BeanFactory；否则初始化一个**`DefaultListableBeanFactory`**，然后序列化，再设置BeanFactory**是否允许Bean覆盖**和是否允许循环使用，最后加载Bean到BeanFactory中

    > `ApplicationContext`继承自`BeanFactory`，实际上是其内部持有一个实例化的`BeanFactory`，以后所有的BeanFactory相关操作都由这个实例来做

    为什么用**`DefaultListableBeanFactory`**，它继承自ConfigurableListableBeanFactory和Autowire...那部分，ConfigurableListableBeanFactory继承了`BeanFactory`的所有三个子类，所以**`DefaultListableBeanFactory`**最牛

  * **`BeanDefinition`**接口定义：

    > Bean可以简单的认为是`BeanDefinition`的实例，`BeanDefinition`里面包含了Bean的信息(指向哪个类、是否单例、是否懒加载、依赖了哪些Bean)

    继承父Bean的配置信息、设置Bean的类名称、是否懒加载、Bean的所有依赖、生成该Bean的工程名称(如果有)、属性值、是否单例等

  * customizeBeanFactory：配置是否允许BeanDefinition覆盖(相同id或name)、是否允许循环引用

  * **loadBeanDefinitions(beanFactory) 方法**：加载各个Bean，并放入BeanFactory中

    给这个BeanFactory实例化一个XmlBeanDefinitionReader



##### Spring如何解决循环依赖

1. 什么是循环依赖？

   循环依赖就是循环引用，A依赖B，B依赖C，C依赖A。

   Spring中循环依赖的场景：构造器循环依赖、Field属性的循环依赖

2. 怎么检测循环依赖？

**SpringBean的加载过程**

代码入口！

```java
ApplicationContext ac = new ClassPathXmlApplicationContext("spring.xml");
ac.getBean(XXX.class);
```

ClassPathXmlApplicationContext是一个加载XML配置文件的类

它的构造方法中的核心方法是`refresh()`方法

>  `refresh`方法是加了`synchronized`对象锁的，好处是避免`close()`方法和`refresh()`方法调用冲突，这里对象锁相当于整个方法加锁，同步范围更小，锁的粒度更小，效率更高

下面来分析`refresh()`中的一些核心方法

**obtainFreshBeanFactory方法**

这个方法是获取刷新Spring上下文的Bean工厂

核心是`obtainFreshBeanFactory()`里的`refreshBeanFactory()` 

中的**`this.loadBeanDefinitions(beanFactory)`**

这个`BeanDefinition`可以看成是IOC过程中的一个产物，可以看成是对Bean定义的抽象，里面的数据是与Bean定义相关的，如属性、初始化方法、销毁方法等

这里的主要方法是`loadBeanDefinitions`，主要做了以下几件事：

1. 初始化了`BeanFactoryReader`
2. 通过`BeanFactoryReader`获取`Resource`，也就是配置文件的位置，将`Resource`转成`Document`对象
3. 将`Document`对象转成容器内部的数据结构(`BeanDefinition`)，也就是解析`Bean`定义的各种元素，转换成`Managed`类(`Spring`对`BeanDefinition`的封装)放在`BeanDefinition`中
4. 解析完后，把解析结果放在`BeanDefinition`对象中，并放到一个`Map`中

以上完成了`BeanDefinition`在`IOC`容器中的注册。本质就是`Spring`加载`XML`配置文件或者注解，解析成`BeanDefinition`

**`Spring`创建`Bean`的过程**

回到`refresh()`方法，刚才的`obtainFreshBeanFactory`方法完成了BeanDefinition的注册，即得到了Bean的相关数据，下面就需要获取`Bean`实例。

这里核心方法是`finishBeanFactoryInitialization`里的`preInstantiateSingletons`里的**`getBean()`**方法

`getBean`方法最终调用的是**`doGetBean`**方法，这里就是依赖注入`DI`发生的地方

**Spring创建好了BeanDefinition之后呢，会开始实例化Bean，并且对Bean的依赖属性进行填充。实例化时底层使用了CGLIB或Java反射技术。上图中instantiateBean和PopulateBean方法很重要！**

**循环依赖问题分析**

在**`DefaultListableBeanFactory`**类中，有如下属性：

```java
一级缓存：
/** 保存所有的singletonBean的实例 */
private final Map<String, Object> singletonObjects = new ConcurrentHashMap<String, Object>(64);

二级缓存：
/** 保存所有早期创建的Bean对象，这个Bean还没有完成依赖注入 */
private final Map<String, Object> earlySingletonObjects = new HashMap<String, Object>(16);
三级缓存：
/** singletonBean的生产工厂*/
private final Map<String, ObjectFactory<?>> singletonFactories = new HashMap<String, ObjectFactory<?>>(16);
 
/** 保存所有已经完成初始化的Bean的名字（name） */
private final Set<String> registeredSingletons = new LinkedHashSet<String>(64);
 
/** 标识指定name的Bean对象是否处于创建状态  这个状态非常重要 */
private final Set<String> singletonsCurrentlyInCreation =
	Collections.newSetFromMap(new ConcurrentHashMap<String, Boolean>(16));

allowEarlyReference是否允许从singletonFactories中通过getObject拿到对象
```

简单来说，一级缓存就是单例对象的缓存，二级缓存就是初始化一半的单例对象，三级缓存是单例对象工厂的缓存

若A依赖B，B又依赖A：

![img](https://user-gold-cdn.xitu.io/2018/11/17/16720b0a8ca086c6?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

**本质就是三级缓存发挥作用，解决了循环**

**getSingleton的过程：**首先从`singletonObject`一级缓存中获取，若没有，则尝试从earlySingletonObjects二级缓存中找，如果没有且允许从singletonFactories通过getObject获取，则去三级缓存中获取，如果获取到了，则移除对应的singletFactory，将singletonObject放到earlySingletonObject，也就是将三级缓存升到二级缓存中





如果A中构造注入了B，那么A在关键的方法`addSingletonFactory`之前就去初始化了B，导致三级缓存中根本没有A，发发生死循环，Spring会抛出异常。

#####  
