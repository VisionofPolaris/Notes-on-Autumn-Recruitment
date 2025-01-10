# Spring框架（Spring-SpringMVC-SpringBoot）

# Spring

## 基础

### Spring、Spring MVC和SpringBoot的区别？

- Spring是集合了多个功能模块的框架，可以帮助进行开发。例如，最重要的模块Spring-core提供了控制反转依赖注入功能的支持。
- SpringMVC是Spring中的一个模块，赋予 Spring 快速构建 MVC 架构的 Web 程序的能力。
- SpringBoot简化了配置。

### Spring中应用的设计模式？

### Spring中常见的模块？

### Spring中常见的注解？

## Spring IoC

### 什么是Spring IoC？

- IoC（Inversion of Control:控制反转）是一种设计思想，将原本在程序中手动创建对象的控制权，交由 Spring 框架来管理。
- 将对象之间的相互依赖关系交给 IoC 容器来管理，并由 IoC 容器完成对象的注入。

### 什么是Spring Bean？

Bean 代指的就是那些被 IoC 容器所管理的对象。通过XML、注解或配置类告诉IoC需要帮助管理哪些对象。

### IoC的实现机制？

简单工厂+配置文件+反射

### BeanFactory是什么？

- BeanFactory是Spring中一个核心的顶级接口。主要负责生产Bean，管理Bean的生命周期。
- 它有非常多的实现类、每个工厂都有不同的职责（单一职责）功能，最强大的工厂是DefaultListableBeanFactory，Spring底层就是使用的该实现工厂进行生产Bean的。

### BeanDefinition是什么？

用于存储Bean的定义信息，如id、全类名、scope、propertyValues等，Bean工厂生产Bean时需要获取Bean的定义信息。

### BeanFactory和ApplicationContext的区别？

- BeanFactory主要负责配置、创建和管理Bean，为Spring提供基本的依赖注入支持。ApplicationContext 是 BeanFactory 的子接口，在 BeanFactory 的基础上添加了其他的面向实际应用的功能支持，如监听、国际化等。（功能）
- BeanFactory是延时加载，也就是说在容器启动时不会注入bean，而是在需要使用bean的时候，才会对该bean进行加载实例化。ApplicationContext 是在容器启动的时候，根据配置文件一次性创建所有的bean，所以运行的时候速度相对BeanFactory比较快。（加载方式）

### BeanFactory和FactoryBean的区别？

- BeanFactory 是 Spring 框架的核心接口之一，用于管理和获取应用程序中的 Bean 实例。它是一个工厂模式的实现，负责创建、配置和管理 Bean 对象。BeanFactory 是 Spring IoC 容器的基础，它可以从配置元数据（如 XML 文件）中读取 Bean 的定义，并在需要时实例化和提供这些 Bean。
- FactoryBean 是一个特殊的 Bean，它是一个工厂对象，用于创建和管理其他 Bean 的实例。FactoryBean 接口定义了一种创建 Bean 的方式，它允许开发人员在 Bean 的创建过程中进行更多的自定义操作。通过实现 FactoryBean 接口，开发人员可以创建复杂的 Bean 实例，或者在 Bean 实例化之前进行一些额外的逻辑处理。

### @Component和@Bean的区别有哪些？

- `@Component` 注解作用于类，通过类路径扫描来自动侦测以及自动装配到 Spring 容器中（`@ComponentScan`）；而`@Bean`注解作用于方法，在标有该注解的方法中定义产生这个 bean。
- `@Bean` 注解比 `@Component` 注解的自定义性更强，而且很多地方我们只能通过 `@Bean` 注解来注册 bean。比如当我们引用第三方库中的类需要装配到 `Spring`容器时，则只能通过 `@Bean`来实现。

### 自动注入Bean的注解有哪些？

常用的有Spring 内置的 `@Autowired` 以及 JDK 内置的 `@Resource` 等。

- `@Autowired` 属于 Spring 内置的注解，可以作用在属性、构造方法、setter方法上。默认的注入方式为`byType`（根据类型进行匹配），也就是说会优先根据接口类型去匹配并注入 Bean （接口的实现类）。如果Spring 同时找到多个满足条件的选择，注入方式会变为 `byName`（根据实现类的名称进行匹配，类名首字母小写）。可以通过 `@Qualifier` 注解来显式指定名称而不是依赖变量的名称。
- `@Resource`属于 JDK 提供的注解，默认注入方式为 `byName`。如果无法通过名称匹配到对应的 Bean 的话，注入方式会变为`byType`。有两个属性：`name`（名称）、`type`（类型）。如果仅指定 `name` 属性则注入方式为`byName`，如果仅指定`type`属性则注入方式为`byType`，如果同时指定`name` 和`type`属性（不建议这么做）则注入方式为`byType`+`byName`。

### 依赖注入的方法有哪些？

- 构造方法注入：通过调用类的构造方法，将接口实现类通过构造方法变量传入。
- 属性注入：通过 Setter 方法完成调用类所需依赖的注入。
- 工厂方法注入
    - 静态工厂注入：就是通过调用静态工厂的方法来获取自己需要的对象，为了让 Spring 管理所有对象，我们不能直接通过"工程类.静态方法()"来获取对象，而是依然通过 Spring 注入的形式获取。
    - 实例工厂注入
        
        工厂方法不是静态的，所以我们需要首先 new 一个工厂实例，再调用普通的实例方法。
        

### Bean的作用域有哪些？

通过`@Scope(value = ConfigurableBeanFactory.SCOPE_PROTOTYPE)` 注解来指定Bean的作用域。

- **singleton** : IoC 容器中只有唯一的 bean 实例。Spring 中的 bean 默认都是单例的，是对单例设计模式的应用。
- **prototype** : 每次获取都会创建一个新的 bean 实例。也就是说，连续 `getBean()` 两次，得到的是不同的 Bean 实例。
- **request** （仅 Web 应用可用）: 每一次 HTTP 请求都会产生一个新的 bean（请求 bean），该 bean 仅在当前 HTTP request 内有效。
- **session** （仅 Web 应用可用） : 每一次来自新 session 的 HTTP 请求都会产生一个新的 bean（会话 bean），该 bean 仅在当前 HTTP session 内有效。
- **application/global-session** （仅 Web 应用可用）：每个 Web 应用在启动时创建一个 Bean（应用 Bean），该 bean 仅在当前应用启动时间内有效。
- **websocket** （仅 Web 应用可用）：每一次 WebSocket 会话产生一个新的 bean。

### Bean是线程安全的吗？

- prototype 作用域下，每次获取都会创建一个新的 bean 实例，不存在资源竞争问题，所以不存在线程安全问题。
- singleton 作用域下，IoC 容器中只有唯一的 bean 实例，可能会存在资源竞争问题（取决于 Bean 是否有状态）。如果这个 bean 是有状态的话，那就存在线程安全问题（有状态 Bean 是指包含可变的成员变量的对象）。

解决有状态的单例Bean线程安全问题：

1. 在 Bean 中尽量避免定义可变的成员变量。
2. 通过`synchronized`或者`Lock`来保证线程安全。
3. 在类中定义一个 `ThreadLocal` 成员变量，将需要的可变成员变量保存在 `ThreadLocal` 中（推荐的一种方式）。

### 单例Bean的优势是什么？

在性能上有优势：

- 减少了生成销毁Bean的开销。
- 直接从缓存里快速获取Bean。

### 了解Bean的生命周期吗？

![spring-bean-lifestyle.png](Spring%E6%A1%86%E6%9E%B6%EF%BC%88Spring-SpringMVC-SpringBoot%EF%BC%89%203c8fc5a173134943af3ee02a597c3d1e/spring-bean-lifestyle.png)

AbstractAutowiredCapableBeanFactory.doCreateBean()

- 创建对象
    - 实例化 构造方法
    - 依赖注入
- 初始化
    - 执行Aware接口
    - 执行BeanPostProcessor.postProcessorBeforeInitialization
    - 执行InitializingBean回调（先执行@PostConstruct）
    - 执行BeanPostProcessor.postProcessorAfterInitialization
- 使用对象
- 销毁对象
    - 执行DisposableBean回调（先执行@preDestory）
    - 单例Bean在IoC容器关闭的时候销毁，多例Bean手动close()。

### 生命周期的回调方法有哪些？

主要有两种，在初始化的时候或在销毁的时候，主要使用方式有3种：

1. 使用注解`@PostConstruct`、`@PreDestroy`。
2. 实现接口`InitializingBean`和`DisposableBean`，分别重写方法`afterPropertiesSet()`和`destory()`。
3. 指定属性`init-method`和`destroy-method`。或使用注解`@Bean(initMethod=”xxx”, destroyMethod=”xxx”)` 。

### Bean的实例化方式有哪些？

1. 构造器方式：通过配置文件或注解，将全类名存在BeanDefinition.beanClass中，通过反射拿到实例；
2. 静态工厂方式：通过factory-method指定静态工厂方法。
3. 实例工厂方式：factory-bean + factory-method / @bean
4. FactoryBean方式：在Bean的基础上实现FactoryBean接口，重写getObject()。

### 如何解决循环依赖？

A依赖B，B依赖A。循环依赖只发生在 Singleton 作用域的 Bean 之间，因为如果是 Prototype 作用域的 Bean，Spring 会直接抛出异常。

Spring可以解决的循环依赖如下，A会先于B创建，如果在构造器注入的话，那么都得在实例化这一步完成注入，而setter 注入实例化和属性赋值是分开的：

![spring-37bb576d-b4af-42ed-91f4-d846ceb012b6.png](Spring%E6%A1%86%E6%9E%B6%EF%BC%88Spring-SpringMVC-SpringBoot%EF%BC%89%203c8fc5a173134943af3ee02a597c3d1e/spring-37bb576d-b4af-42ed-91f4-d846ceb012b6.png)

采用三级缓存，提前暴露单例对象：

1. 一级缓存 : `Map<String,Object>` **singletonObjects**，单例池，用于保存实例化、属性赋值（注入）、初始化完成的 bean 实例。
2. 二级缓存: `Map<String,Object>`  **earlySingletonObjects**，早期曝光对象，用于保存实例化完成的 bean 实例。
3. 三级缓存 : `Map<String,ObjectFactory<?>>` **singletonFactories**，早期曝光对象工厂，用于保存 bean 创建工厂，以便后面有机会创建代理对象。

过程：

1. 创建 A 实例，实例化的时候把 A 的对象工厂放⼊三级缓存，表示 A 开始实例化了，虽然我这个对象还不完整，但是先曝光出来让大家知道。
2. A 注⼊属性时，发现依赖 B，此时 B 还没有被创建出来，所以去实例化 B。
3. 同样，B 注入属性时发现依赖 A，它就从缓存里找 A 对象。依次从一级到三级缓存查询 A。把 A 放⼊二级缓存，同时删除三级缓存中的 A，此时，B 已经实例化并且初始化完成了，把 B 放入一级缓存。
4. 接着 A 继续属性赋值，顺利从一级缓存拿到实例化且初始化完成的 B 对象，A 对象创建也完成，删除二级缓存中的 A，同时把 A 放⼊一级缓存。

### 只使用两级缓存能不能解决循环依赖？

[spring为什么要使用三级缓存解决循环依赖 - 简书 (jianshu.com)](https://www.jianshu.com/p/84fc65f2764b)

Bean的创建过程包括实例化-属性注入-初始化。正常来说，代理对象的创建是在初始化阶段BeanPostProcessor完成的，如果发生了循环依赖，需要把代理对象的创建提前。只是用两级缓存无法解决代理对象相互依赖的问题。

## Spring AOP

### AOP的了解？

- AOP面向切面编程，使与业务无关，但对多个对象产生影响的公共行为和逻辑（如日志、事务处理等）抽取出来并封装成一个可重用的模块。可以使核心业务与非核心业务代码进行分离，降低模块间的耦合性，提高系统可维护性。
- OOP将业务处理的实体的属性和行为进行抽象封装，获得更加清晰的逻辑单元划分。AOP本身是OOP的补充，对业务处理中的切面进行提取，隔离公共逻辑与业务逻辑。

### Spring AOP和Aspect AOP的区别？

- 在spring中使用@Aspect、@Before等注解需要添加AspectJ的依赖。Spring AOP提供了AspectJ的支持，但只用到了AspectJ的切点解析和匹配。AspectJ 相比于 Spring AOP 功能更加强大，但是 Spring AOP 相对来说更简单。
- Spring AOP使用动态代理在运行时增强，有接口用JDK实现，无接口用CGLib实现。AspectJ使用静态代理在编译时增强。
- AspectJ提供了AOP编程的完全解决方案，且在代码运行前完成织入，运行时不需要生成类的开销，性能更好。当切面太多的话，使用Aspect更快。

### AOP核心概念有哪些？

- **通知（Advice）**，切面必须要完成的各个具体工作，根据代码的位置分为：
    - @Before：通知方法会在目标方法调用之前执行
    - @After：通知方法会在目标方法调用后执行
    - @AfterReturning：通知方法会在目标方法返回后执行
    - @AfterThrowing：通知方法会在目标方法抛出异常后执行
    - @Around：把整个目标方法包裹起来，在被调用前和调用之后分别执行通知方法
- **切面（Aspect）**，对横切关注点进行封装的类，管理公共业务逻辑（通知）和切入方式（切点）；通常使用 @Aspect 注解来定义切面。
- **连接点（JoinPoint）**，被增强的业务方法。
- **切点（Pointcut）**，通知功能被应用的范围，决定哪些方法需要增强，根据切入点表达式决定。
    
    ```java
    @Pointcut("execution(void com.itheima.dao.UserDao.save())") // 切入点表达式
    public void pt() {}
    ```
    

### JDK动态代理和CGLIB动态代理的区别？

- JDK只提供接口的代理，不支持类的代理。实现了目标类的接口，代理类会实现接口所有的方法增强代码。调用时先去调用处理类进行增强，再通过反射的方式进行调用目标方法。
- 如果代理类没有实现接口，那么Spring AOP会选择使用CGLIB来动态代理目标类。通过ASM在运行时动态的生成目标类的一个子类（以及其他的相关类），重写父类所有的方法增强代码。调用时先通过代理类进行增强，再直接调用父类对应的方法进行调用目标方法。

### Spring AOP在哪里创建动态代理？

- 在Bean生命周期的初始化后，通过BeanPostProcessor创建。
- 在发生循环依赖时，会提前创建AOP。

### AOP的完整实现流程？

Aop的实现大致分为三大步：

当@EnableAspectJAutoProxy会通过@lmport注册一个BeanPostProcessor处理AOP

1. 在创建Bean时调用BeanPostProcessor解析切面@Aspect，将切面中所有的通知解析为advisor（该对象包含通知、切点、通知方法）排好序放入List并缓存。
2. 在Bean初始化后调用BeanPostProcessor拿到之前缓存的advisor判断当前Bean是否被切点表达式命中，如果匹配为Bean创建动态代理。
3. 调用：通过之前创建的动态代理调用方法执行增强，通过调用链设计模式依次调用通知方法。

## Spring事务

### Spring支持哪两种事务管理方式？

- 编程式事务管理：
    - 通过 `TransactionTemplate`或者`TransactionManager`手动管理事务。
        
        ```java
        @Autowired
        private TransactionTemplate transactionTemplate;
        public void testTransaction() {
        
                transactionTemplate.execute(new TransactionCallbackWithoutResult() {
                    @Override
                    protected void doInTransactionWithoutResult(TransactionStatus transactionStatus) {
                        try {
                            // ....  业务代码
                        } catch (Exception e){
                            //回滚
                            transactionStatus.setRollbackOnly();
                        }
        
                    }
                });
        }
        ```
        
- 声明式事务管理：
    - 通过 AOP 实现，代码侵入小，基于`@Transactional` 的全注解方式使用最多。
        
        ```java
        @Transactional(propagation = Propagation.REQUIRED)
        public void aMethod {
          //do something
          B b = new B();
          C c = new C();
          b.bMethod();
          c.cMethod();
        }
        ```
        

### Spring事务管理接口

Spring 框架中，事务管理相关最重要的 3 个接口如下：

- **`PlatformTransactionManager`**：（平台）事务管理器，Spring 事务策略的核心。定义了获取事务、提交事务、回滚事务的方法，抽象出事务管理的行为，由不同的平台去实现。
    
    ```java
    package org.springframework.transaction;
    import org.springframework.lang.Nullable;
    public interface PlatformTransactionManager {
        //获得事务
        TransactionStatus getTransaction(@Nullable TransactionDefinition var1) throws TransactionException;
        //提交事务
        void commit(TransactionStatus var1) throws TransactionException;
        //回滚事务
        void rollback(TransactionStatus var1) throws TransactionException;
    }
    ```
    
- **`TransactionDefinition`**：事务定义信息（事务隔离级别、传播行为、超时、只读、回滚规则（遇到什么异常时回滚））。
- **`TransactionStatus`**：事务运行状态。

### 事务传播行为有哪些？

事务传播行为是为了解决业务层方法之间互相调用的事务问题。

- **`TransactionDefinition.PROPAGATION_REQUIRED`**
    - 如果外部方法没有开启事务的话，`Propagation.REQUIRED`修饰的内部方法会新开启自己的事务，且开启的事务相互独立，互不干扰。
    - 如果外部方法开启事务并且被`Propagation.REQUIRED`的话，所有`Propagation.REQUIRED`修饰的内部方法和外部方法均属于同一事务 ，只要一个方法回滚，整个事务均回滚。
- **`TransactionDefinition.PROPAGATION_REQUIRES_NEW`**
    - 创建一个新的事务，如果当前存在事务，则把当前事务挂起。不管外部方法是否开启事务，`Propagation.REQUIRES_NEW`修饰的内部方法会新开启自己的事务，且开启的事务相互独立，互不干扰。
- **`TransactionDefinition.PROPAGATION_NESTED`**
    - 在外部方法开启事务的情况下，在内部开启一个新的事务，作为嵌套事务存在。
    - 如果外部方法无事务，则单独开启一个事务，与 `PROPAGATION_REQUIRED` 类似。
- **`TransactionDefinition.PROPAGATION_SUPPORTS`**: 如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务的方式继续运行。
- **`TransactionDefinition.PROPAGATION_NOT_SUPPORTED`**: 以非事务方式运行，如果当前存在事务，则把当前事务挂起。
- **`TransactionDefinition.PROPAGATION_MANDATORY`**
    - 如果当前存在事务，则加入该事务；如果当前没有事务，则抛出异常。
- **`TransactionDefinition.PROPAGATION_NEVER`**: 以非事务方式运行，如果当前存在事务，则抛出异常。

### 事务的隔离级别有哪些？

- **`TransactionDefinition.ISOLATION_DEFAULT`** :使用后端数据库默认的隔离级别，MySQL 默认采用的 `REPEATABLE_READ` 隔离级别 Oracle 默认采用的 `READ_COMMITTED` 隔离级别
- **`TransactionDefinition.ISOLATION_READ_UNCOMMITTED`** :最低的隔离级别，使用这个隔离级别很少，因为它允许读取尚未提交的数据变更，**可能会导致脏读、幻读或不可重复读**
- **`TransactionDefinition.ISOLATION_READ_COMMITTED`** : 允许读取并发事务已经提交的数据，**可以阻止脏读，但是幻读或不可重复读仍有可能发生**
- **`TransactionDefinition.ISOLATION_REPEATABLE_READ`** : 对同一字段的多次读取结果都是一致的，除非数据是被本身事务自己所修改，**可以阻止脏读和不可重复读，但幻读仍有可能发生。**
- **`TransactionDefinition.ISOLATION_SERIALIZABLE`** : 最高的隔离级别，完全服从 ACID 的隔离级别。所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰，也就是说，**该级别可以防止脏读、不可重复读以及幻读**。但是这将严重影响程序的性能。通常情况下也不会用到该级别。

### 事务失效的原因？

- `@Transactional` 注解只有作用到 public 方法上事务才生效，不推荐在接口上使用；
- 避免同一个类中调用 `@Transactional` 注解的方法，这样会导致事务失效；
- 被 `@Transactional` 注解的方法所在的类必须被 Spring 管理，否则不生效；
- 底层使用的数据库必须支持事务机制，否则不生效；
- ……·

# SpringMVC

## SpringMVC的核心组件有哪些？

- **`DispatcherServlet`**：**核心的中央处理器**，负责接收请求、分发，并给予客户端响应；
- **`HandlerMapping`**：**处理器映射器**，根据 URL 去匹配查找能处理的 `Handler` ，并会将请求涉及到的拦截器和 `Handler` 一起封装；
- **`HandlerAdapter`**：**处理器适配器**，根据 `HandlerMapping` 找到的 `Handler` ，适配执行对应的 `Handler`；
- **`Handler`**：**请求处理器**，处理实际请求的处理器；
- **`ViewResolver`**：**视图解析器**，根据 `Handler` 返回的逻辑视图 / 视图，解析并渲染真正的视图，并传递给 `DispatcherServlet` 响应客户端。

### 为什么有了HandlerAdapter还需要Handler？

Spring MVC 允许使用多种类型的处理器。不仅仅是标准的`@Controller`注解的类，还可以是实现了特定接口的其他类（如 HttpRequestHandler 或 SimpleControllerHandlerAdapter 等）。这些处理器可能有不同的方法签名和交互方式。

HandlerAdapter 的主要职责就是调用 Handler 的方法来处理请求，并且适配不同类型的处理器。HandlerAdapter 确保 DispatcherServlet 可以以统一的方式调用不同类型的处理器，无需关心具体的执行细节。

## SpringMVC的执行流程？

![de6d2b213f112297298f3e223bf08f28.png](Spring%E6%A1%86%E6%9E%B6%EF%BC%88Spring-SpringMVC-SpringBoot%EF%BC%89%203c8fc5a173134943af3ee02a597c3d1e/de6d2b213f112297298f3e223bf08f28.png)

使用了SpringMVC之后，所有的请求都需要经过DispatcherServlet前端控制器，该类中提供了一个doDispatch方法，请求的分发和结果的响应都在该方法中完成：

1. 借助HandlerMapping处理器映射器得到处理器执行链，包含了目标Controller的方法HandlerMethod以及要执行的拦截器。
2. 根据HandlerMethod获取对应的处理器适配器HandlerAdapter，封装了参数解析器以及结果处理器。
3. 执行拦截器的PreHandler方法。
4. 通过HandlerAdapter执行目标Controller方法，通过参数解析器和结果处理器分别解析浏览器提交的数据以及处理Controller方法返回的结果。
5. 执行拦截器的PostHandler方法。
6. 处理响应，如果有异常抛出会执行全局异常处理器逻辑，并通过视图解析器ViewResolver解析视图，再渲染视图，最后再执行拦截器的afterCompletion。

# SpringBoot

## 基础

### SpringBoot有哪些特性？

SpringBoot是Spring提供的一个子项目，用于快速构建Spring应用程序。传统的方式构建Spring应用程序，导入依赖、项目配置繁琐。

1. 提供起步依赖，根据Maven的传递依赖特性，将项目开发必备的依赖通过一个Maven坐标导入。
    
    ```xml
    <parent>
    	<groupId>org.springframework.boot</groupId>
    	<artifactId>spring-boot-starter-web</artifactId>
    	<version>3.1.2</version>
    </parent>
    
    <dependencies>
    	<dependency>
    		<groupId>org.springframework.boot</groupId>
    		<artifactId>spring-boot-starter-web</artifactId>
    	</dependency>
    </dependencies>
    ```
    
2. 提供自动配置，在boot程序启动之后，一些bean对象会自动注入到IoC容器中，不需要手动声明，简化开发。
3. 其他特性：
    1. 内嵌Tomcat、Jetty，无需部署war文件
    2. 外部化配置
    3. 不需要XML配置

### 配置文件的优先级？

- 配置文件的优先级顺序为properties > yml > yaml。共存叠加，相互覆盖。
- 位置优先级：根目录Config目录>根目录>resource目录config目录>resource目录。

### 如何获取配置信息？

配置信息包括：

1. 第三方技术配置信息（如Redis、Mybatis等）：直接引入起步依赖，在配置文件中进行配置，会自动读取。
2. 自定义配置信息：在配置文件中书写配置，通过`@Value(”${xxx}”)`或者`@ConfigurationProperties(prefix=”xxx”)` 读取配置。
    
    ![Untitled](Spring%E6%A1%86%E6%9E%B6%EF%BC%88Spring-SpringMVC-SpringBoot%EF%BC%89%203c8fc5a173134943af3ee02a597c3d1e/Untitled.png)
    

## Bean管理

### Bean扫描

- `@SpringBootApplication`注解包含了`@ComponentScan`注解。扫描的路径是默认是启动类所在的包及其子包。
- 要扫描其他的包需要手动在启动类上添加扫描路径`@ComponentScan(basePackages = “xxx”)` 。

### Bean注册

引入第三方Bean，无法用`@Component` 声明Bean。

- 建立配置类，用`@Bean`注解方法，方法返回值为注册的bean对象。对象默认的名字为方法的名字。如果方法的内部需要使用到IoC容器中已经存在的Bean对象，那么只需要在方法参数中引入。
    
    ![Untitled](Spring%E6%A1%86%E6%9E%B6%EF%BC%88Spring-SpringMVC-SpringBoot%EF%BC%89%203c8fc5a173134943af3ee02a597c3d1e/Untitled%201.png)
    
- 通过`@Import(xxx.class)`注解实现。
- 通过`@Import(xxxImportSelector.class)`注解实现。
    
    ![Untitled](Spring%E6%A1%86%E6%9E%B6%EF%BC%88Spring-SpringMVC-SpringBoot%EF%BC%89%203c8fc5a173134943af3ee02a597c3d1e/Untitled%202.png)
    

### Bean注册的条件

可以设置注册的条件`@Conditional`：

![Untitled](Spring%E6%A1%86%E6%9E%B6%EF%BC%88Spring-SpringMVC-SpringBoot%EF%BC%89%203c8fc5a173134943af3ee02a597c3d1e/Untitled%203.png)

![Untitled](Spring%E6%A1%86%E6%9E%B6%EF%BC%88Spring-SpringMVC-SpringBoot%EF%BC%89%203c8fc5a173134943af3ee02a597c3d1e/Untitled%204.png)

![Untitled](Spring%E6%A1%86%E6%9E%B6%EF%BC%88Spring-SpringMVC-SpringBoot%EF%BC%89%203c8fc5a173134943af3ee02a597c3d1e/Untitled%205.png)

## 高级

### 自动配置

![Untitled](Spring%E6%A1%86%E6%9E%B6%EF%BC%88Spring-SpringMVC-SpringBoot%EF%BC%89%203c8fc5a173134943af3ee02a597c3d1e/Untitled%206.png)

![Untitled](Spring%E6%A1%86%E6%9E%B6%EF%BC%88Spring-SpringMVC-SpringBoot%EF%BC%89%203c8fc5a173134943af3ee02a597c3d1e/Untitled%207.png)

- 首先，在主启动类上添加了SpringBootApplication注解，这个注解组合了EnableAutoConfiguration注解，开启自动配置。
- 其次，EnableAutoConfiguration注解又组合了Import注解，导入了AutoConfigurationImportSelector类。该类实现了selectImports方法，这个方法经过层层调用，会读取META-INF目录下的后缀为imports的文件（2.7版本之前读的是spring.factories文件）。文件中记录了很多全类名。
- 最后，读到全类名之后，会解析注册条件（@Conditional及其衍生注解），将满足注册条件的Bean对象自动注入到IoC容器中。

### 自定义starter

- 在项目开发中，常常会定义一些公共组件，提供给各个项目团队使用。而在SpringBoot的项目中，一般会将这些公共组件封装为SpringBoot的Starter。
- 一般起步依赖由两个工程组成，一个提供自动管理功能，一个提供依赖管理功能。会在starter中引入autoconfigure。

## 项目部署

### 项目部署的流程？

1. 配置打包插件
    
    ```xml
    <build>
    		<plugins>
    				<plugin>
    						<groupId>org.springframework.boot</groupId>
    						<artifactId>spring-boot-maven-plugin</artifactId>
    						<version>x.x.x</version>
    				</plugin>
    		</plugins>
    </build>
    ```
    
2. maven生命周期，打包package
3. 运行生成的.jar文件，运行的服务器必须有jre环境
    
    ```bash
    java -jar xxx.jar [--server.port=8090]
    ```
    
    **属性参数优先级**：命令行参数>系统环境变量>jar包所在目录下的application.yml文件>源码
    

### 多环境开发的方法？

![Untitled](Spring%E6%A1%86%E6%9E%B6%EF%BC%88Spring-SpringMVC-SpringBoot%EF%BC%89%203c8fc5a173134943af3ee02a597c3d1e/Untitled%208.png)

![Untitled](Spring%E6%A1%86%E6%9E%B6%EF%BC%88Spring-SpringMVC-SpringBoot%EF%BC%89%203c8fc5a173134943af3ee02a597c3d1e/Untitled%209.png)

# 源码问题

## 前置知识

### ApplicationContextInitializer

IoC容器对象创建完成之后执行，可以对上下文环境做一些操作，例如运行环境属性注册等。

1. 自定义类，实现ApplicationContextInitializer接口，重写initialize方法。
2. 在META-INF/Spring.factories配置文件中配置自定义的类。org.springframework.context.ApplicationContextInitializer=自定义类全类名

### ApplicationListener

监听容器发布的事件，允许程序员完成自己的代码，完成事件驱动开发，可以监听容器初始化完成、初始化失败等事件。通常可以使用监听器加载资源，开启定时任务等。

容器发布的事件包括：

- 容器初始化成功：ApplicationReadyEvent
- 容器初始化失败：ApplicationFailedEvent
1. 自定义类，实现ApplicationListener接口，重写onApplicationEvent方法。
2. 在META-INF/Spring.factories配置文件中配置自定义的类。

### BeanFactory

Bean容器的顶级接口，提供Bean对象的创建、配置、依赖注入等功能。

最终得到的容器类型是AnnotationConfigServletWebServerApplicationContext，但其中的很多方法是委托给父类来执行的。例如，getBean()是委托给DefaultListableBeanFactory来执行的。

### BeanDefinition

用于描述Bean，包括Bean的名称、属性、行为、实现的接口、添加的注解等。Spring中，Bean在创建之前，都需要封装成对应的BeanDefinition，然后根据BeanDefinition进一步创建Bean对象。

通过@Component系列注解创建的Bean会用ScannedGenericBeanDefinition描述，通过@Bean注解创建的Bean会用ConfigurationClassBeanDefinition描述。

### BeanFactoryPostProcessor

Bean工厂的后置处理器，当BeanFactory准备好了以后，（Bean初始化之前），会调用该接口的postProcessorBeanFactory方法，经常用于新增BeanDefinition。

### Aware

通过感知接口，可以感知Spring应用程序执行过程中的一些变化。Spring会判断当前Bean是否实现Aware接口，会在特定的时机回调接口对应的方法。

- 感知Bean名称的子接口BeanNameAware
- 感知类加载器的子接口BeanClassLoaderAware
- 感知Bean工厂的子接口BeanFactoryAware
1. 自定义类，重写子接口，重写setBeanFactory方法。
2. 通过@Component注入容器。

### InitializingBean/DisposableBean

- 初始化接口，Bean实例化好后回调里面的函数，一般做资源加载的工作
- 销毁接口，在Bean销毁之前回调函数，一般做资源释放工作
1. 自定义类，重写接口，重写afterPropertiesSet/destory方法。
2. 通过@Component注入容器。

> PostConstructor和PreDestory两个注解也可以提供初始化和销毁方法。这两个注解都先于两个接口执行。
> 

### BeanPostProcessor

Bean后置处理器，当Bean对象初始化之前以及初始化之后，会回调该接口对应的方法。

- Bean对象初始化之前调用：postProcessBeforeInitialization
- Bean对象初始化之后调用：postProcessAfterInitialization

## 基本问题

### SpringBoot的启动流程？

- new SpringApplication()
    - 确认web应用的类型，”servlet”为传统的web应用
    - 加载ApplicationContextInitializer
    - 加载ApplicationLisener
    - 记录主启动类
- run()
    - 准备环境对象Environment，用于加载系统属性等等
    - 打印Banner
    - 实例化容器Context
    - 准备容器，为容器设置Environment、BeanFactoryPostProcessor，并加载主类对应的BeanDefinition
    - 刷新容器（创建Bean实例）
    - 返回容器

![Untitled](Spring%E6%A1%86%E6%9E%B6%EF%BC%88Spring-SpringMVC-SpringBoot%EF%BC%89%203c8fc5a173134943af3ee02a597c3d1e/Untitled%2010.png)

SpringBoot启动，本质上就是加载各种信息，然后初始化IoC容器并返回。在启动类执行SpringApplication.run方法时，在它的内部会创建SpringApplication对象并执行run方法。

首先，在初始化SpringApplication对象时，主要会进行三件事情：（1）确认web应用的类型，一般情况下是“servlet”，这种类型的应用将来会自动启动一个tomcat；（2）从spring.factories配置文件中，加载默认的ApplicationContextInitializer和ApplicationListener；（3）记录当前应用的主启动类，将来做包扫描用。

其次，在对象创建好了之后，在调用对象的run方法，主要会做四件事情，（1）准备好Environment对象，会封装一些当前应用运行环境的参数，比如环境变量等等；（2）实例化容器，创建ApplicationContext对象；（3）容器创建好了之后，会做一些准备工作，比如为容器设置Environment、BeanFactoryPostProcessor后置处理器，并加载主类对应的Definition；（4）最后刷新容器，真正创建Bean实例并返回容器。

### IoC容器的初始化流程？

AbstractApplicationContext.refresh()

- 准备BeanFactory（DefaultListableBeanFactory）
    - 设置ClassLoader
    - 设置Environment
- 扫描要放入容器的Bean，得到对应的BeanDefinition（只扫描，不创建）
- 注册BeanPostProcessor
- 处理国际化
- 初始化事件多播器ApplicationEventMulticaster
- 启动tomcat
- 绑定事件监听器和事件多播器
- 实例化非懒加载的单例Bean
- 扫尾工作，如清空实例化时占用的缓存

![Untitled](Spring%E6%A1%86%E6%9E%B6%EF%BC%88Spring-SpringMVC-SpringBoot%EF%BC%89%203c8fc5a173134943af3ee02a597c3d1e/Untitled%2011.png)

IoC容器的初始化，核心工作是在AbstractApplicationContext.refresh()中实现的，主要做的工作包括：

1. 准备BeanFactory，需要为BeanFactory设置很多属性，包括类加载器、Environment等；
2. 执行BeanFactory后置处理器，会扫描要放入到容器中的Bean信息，得到对应的BeanDefinition；
3. 注册BeanPostProcessor，自定义的BeanPostProcessor就是在这个阶段被加载的，将来Bean对象实例化好后需要用到；
4. 启动Tomcat；
5. 实例化非懒加载的单例Bean，多例Bean和懒加载的单例Bean将来用到再创建；
6. 容器初始化结束后，再做一些扫尾的工作，比如清除缓存等。

总结来说，再IoC容器初始化的过程中，首先应准备并执行BeanFactory后置处理器，然后注册Bean后置处理器并启动Tomcat，最后需要借助BeanFactory完成Bean的实例化。

### Bean的循环依赖？

IOC容器→DefaultListableBeanFactory→DefaultSingletonRegistry，在DefaultSingletonRegistry中有三级缓存：

- singletonObjects：完整的Bean
- earlySingletonObjects：半成品Bean
- singletonFactories：ObjectFactory工厂