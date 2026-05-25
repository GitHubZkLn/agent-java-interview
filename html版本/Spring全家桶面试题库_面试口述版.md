# Spring / Spring Boot 全家桶 — 深度面试题库 · 面试口述版

---

## 文档说明

> **目标知识点：** Spring IOC/AOP 原理、Bean 生命周期、循环依赖三级缓存、Spring Boot 自动配置/Starter 机制、Spring 事务（传播行为/失效场景）、Spring MVC 请求处理流程、设计模式（策略/工厂/模板方法/单例/观察者）、Filter/Interceptor/AOP 区别、Spring 常用注解对比。
>
> **格式说明：** 每道题分为【面试口述版】（直接口头回答）和【追问扩展】（面试官追问方向）。追问补充单独成篇附在正文后。
>
> **关键词标注：** ⭐ 表示面试高频追问点，需重点准备。

---

## 目录

- [第一部分：Spring IOC 与依赖注入](#第一部分spring-ioc-与依赖注入)
  - [Q1 Spring 核心模块架构](#q1-spring-核心模块架构)
  - [Q2 Spring IOC 原理：控制反转到底是什么？](#q2-spring-ioc-原理控制反转到底是什么)
  - [Q3 依赖注入的三种方式对比](#q3-依赖注入的三种方式对比)
  - [Q4 BeanFactory 和 ApplicationContext 的区别](#q4-beanfactory-和-applicationcontext-的区别)
  - [Q5 Bean 的作用域（scope）](#q5-bean-的作用域scope)
  - [Q6 Spring Bean 生命周期（14 个步骤）](#q6-spring-bean-生命周期14-个步骤)
  - [Q7 Spring 常用注解对比](#q7-spring-常用注解对比)
- [第二部分：Spring AOP 原理](#第二部分spring-aop-原理)
  - [Q8 AOP 核心概念（切面/切点/通知/连接点/织入）](#q8-aop-核心概念切面切点通知连接点织入)
  - [Q9 JDK 动态代理 vs CGLIB 的区别与选择策略](#q9-jdk-动态代理-vs-cglib-的区别与选择策略)
  - [Q10 Spring AOP 源码流程（ProxyFactory / Advisor / MethodInterceptor）](#q10-spring-aop-源码流程proxyfactory--advisor--methodinterceptor)
  - [Q11 五种通知的执行顺序](#q11-五种通知的执行顺序)
- [第三部分：Spring 循环依赖与三级缓存](#第三部分spring-循环依赖与三级缓存)
  - [Q12 循环依赖的本质与三种类型](#q12-循环依赖的本质与三种类型)
  - [Q13 三级缓存原理](#q13-三级缓存原理)
  - [Q14⭐ 为什么需要三级缓存而不是二级？AOP 冲突如何解决？](#q14-为什么需要三级缓存而不是二级aop-冲突如何解决)
  - [Q15⭐ 构造器循环依赖为什么无法解决？](#q15-构造器循环依赖为什么无法解决)
- [第四部分：Spring Boot 核心机制](#第四部分spring-boot-核心机制)
  - [Q16 Spring Boot 启动流程（10 个关键步骤）](#q16-spring-boot-启动流程10-个关键步骤)
  - [Q17⭐ Spring Boot 自动配置原理](#q17-spring-boot-自动配置原理)
  - [Q18 Spring Boot Starter 原理](#q18-spring-boot-starter-原理)
  - [Q19 Spring Boot 内嵌 Tomcat 原理](#q19-spring-boot-内嵌-tomcat-原理)
- [第五部分：Spring 事务](#第五部分spring-事务)
  - [Q20⭐ @Transactional 事务失效的经典场景（8 种）](#q20-transactional-事务失效的经典场景8-种)
  - [Q21 事务的七种传播行为](#q21-事务的七种传播行为)
  - [Q22 Spring 事务隔离级别](#q22-spring-事务隔离级别)
- [第六部分：Spring MVC 与请求处理](#第六部分spring-mvc-与请求处理)
  - [Q23⭐ Spring MVC 九大组件与请求处理流程](#q23-spring-mvc-九大组件与请求处理流程)
  - [Q24 @RequestMapping 的工作原理](#q24-requestmapping-的工作原理)
- [第七部分：Spring 设计模式实战](#第七部分spring-设计模式实战)
  - [Q25 策略模式在 Spring 中的应用](#q25-策略模式在-spring-中的应用)
  - [Q26 工厂模式：BeanFactory 与 FactoryBean](#q26-工厂模式beanfactory-与-factorybean)
  - [Q27 模板方法模式：JdbcTemplate / RestTemplate](#q27-模板方法模式jdbctemplate-resttemplate)
  - [Q28 单例模式：Spring Bean 默认单例的原因](#q28-单例模式spring-bean-默认单例的原因)
  - [Q29 观察者模式：ApplicationEvent 与 ApplicationListener](#q29-观察者模式-applicationevent-与-applicationlistener)
  - [Q30 AOP 代理模式：JdkDynamicAopProxy / ObjenesisCglibAopProxy](#q30-aop-代理模式jdkdynamicaopproxy--objenesiscglibaopproxy)
  - [Q31 装饰器模式：HttpServletRequestWrapper / ResponseWrapper](#q31-装饰器模式-httpservletrequestwrapper--responsewrapper)
- [第八部分：Filter / Interceptor / AOP 对比](#第八部分filter-interceptor-aop-对比)
  - [Q32 Filter / Interceptor / AOP 执行顺序与区别](#q32-filter-interceptor-aop-执行顺序与区别)
- [追问补充篇：各知识点高频追问](#追问补充篇各知识点高频追问)

---

---

# 第一部分：Spring IOC 与依赖注入

---

## Q1：Spring 核心模块架构

### 面试口述版

> Spring 是一个生态非常庞大的框架，核心由多个模块组成。
>
> **Spring Framework 核心模块：**
>
> - **spring-core**：Spring 的核心工具类，包括 IOC 容器和工具类（StringUtils、ReflectionUtils 等）
> - **spring-beans**：BeanFactory 和 Bean 的定义、创建、依赖注入，是 IOC 的基础
> - **spring-context**：ApplicationContext 的实现，提供了国际化、事件传播、资源加载等企业级功能
> - **spring-aop**：AOP 面向切面编程的实现，是 Spring 事务的基础
> - **spring-expression**：SpEL 表达式语言，用于在配置中动态计算
>
> **Web 层：**
>
> - **spring-web**：Servlet 封装、文件上传、REST 风格 URL 映射
> - **spring-webmvc**：Spring MVC 框架，包含 DispatcherServlet 和视图解析
>
> **数据层：**
>
> - **spring-jdbc**：JDBC 封装，提供了 JdbcTemplate
> - **spring-tx**：声明式事务管理
> - **spring-orm**：整合 Hibernate、JPA 等 ORM 框架
>
> **集成层：**
>
> - **spring-test**：单元测试支持，@SpringBootTest 等
>
> **Spring Boot 的模块化设计：**
>
> Spring Boot 在 Spring Framework 基础上，通过 spring-boot-starter 机制进一步简化了依赖管理。Spring Boot 不是替代 Spring Framework，而是对 Spring 的封装和增强。

---

## Q2：Spring IOC 原理：控制反转到底是什么？

### 面试口述版

> **IOC = Inversion of Control，控制反转。**
>
> 传统开发中，对象的创建和依赖管理由程序员手动完成——A 需要用 B，程序员手动 new B()，并通过 setter 传入 A。对象之间的依赖关系是写死在代码里的。
>
> **IOC 的本质：** 把对象的创建和依赖关系的维护，从程序员手中"反转"交给 Spring 容器。
>
> **用一个生活例子理解：** 传统方式是"自己做饭自己吃"——你需要自己种菜、买菜、做饭。IOC 方式是"去餐厅点餐"——你只管点菜（声明依赖），厨师做好后端上来（容器注入）。你不知道厨师是谁、怎么做的，但菜就是到你手上了。
>
> **IOC 的两种实现方式：**
>
> - **依赖注入（DI，Dependency Injection）**：运行时由容器通过构造函数、setter 或字段注入依赖。这是主流方式。
> - **依赖查找（DL，Dependency Lookup）**：对象主动向容器请求依赖。现在很少直接用。
>
> **IOC 的核心价值：**
>
> - **解耦**：对象不需要知道依赖的实现类，只声明接口即可
> - **可测试性**：可以通过 mock 对象轻松替换真实依赖
> - **可复用性**：组件不再绑定具体的实现类，可以在不同项目间复用
>
> **面试加分点：** Spring IOC 容器初始化时，会扫描所有标注了 @Component 等注解的类，解析类之间的依赖关系，构建一张"Bean 依赖图"。这个依赖图就是 Spring 管理对象关系的核心。

---

## Q3：依赖注入的三种方式对比

### 面试口述版

> Spring 支持三种依赖注入方式：构造器注入、Setter 注入、字段注入（@Autowired 字段注入）。
>
> **构造器注入：**
>
> ```java
> @Service
> public class OrderService {
>     private final UserService userService;
>
>     public OrderService(UserService userService) {  // 构造器注入
>         this.userService = userService;
>     }
> }
> ```
>
> **Setter 注入：**
>
> ```java
> @Service
> public class OrderService {
>     private UserService userService;
>
>     @Autowired
>     public void setUserService(UserService userService) {  // Setter注入
>         this.userService = userService;
>     }
> }
> ```
>
> **字段注入：**
>
> ```java
> @Service
> public class OrderService {
>     @Autowired
>     private UserService userService;  // 字段注入（不推荐）
> }
> ```
>
> **为什么 Spring 官方推荐构造器注入？**
>
> 七个理由：
>
> **第一，依赖不可变性。** 构造器注入可以声明为 final，确保线程安全；Setter 注入和字段注入无法声明 final。
>
> **第二，完全初始化保证。** 构造器注入保证对象创建时所有依赖都已就绪，不可能出现 NPE（空指针异常）；后两种方式在对象创建时依赖可能为 null。
>
> **第三，强制依赖契约。** 明确表达"没有这个依赖，这个类就无法工作"。Setter 注入暗示依赖是可选的。
>
> **第四，与容器解耦。** 构造器注入的对象可以在 new 时直接传入 mock 对象，无需 Spring 容器即可单元测试。
>
> **第五，快速暴露循环依赖。** 构造器注入在启动时就暴露循环依赖问题；字段注入会隐藏这个问题，直到运行时才报错。
>
> **第六，代码可读性。** 通过构造器参数，类的所有依赖一目了然。字段注入的依赖分散在类各处，隐藏了类的真实依赖。
>
> **第七，Spring 4.3+ 隐式构造器注入。** 当类只有一个构造器时，Spring 自动作为注入构造器，不需要 @Autowired。
>
> **面试加分点：** 字段注入虽然写起来最简单，但无法做单元测试（需要反射或容器），也违背了"依赖应当通过构造器表达"的原则。在 Spring 官方眼里，字段注入是"反模式"。

---

## Q4：BeanFactory 和 ApplicationContext 的区别

### 面试口述版

> BeanFactory 和 ApplicationContext 都是 Spring IOC 容器的接口，ApplicationContext 是 BeanFactory 的子接口。
>
> **BeanFactory 的特点：**
>
> - 是最底层的 IOC 容器接口
> - 采用**懒加载**——Bean 在第一次被请求时才创建
> - 只提供基本的 Bean 创建和依赖注入功能
> - 适合资源敏感的环境（内存受限的嵌入式设备）
>
> **ApplicationContext 的特点：**
>
> - 继承自 BeanFactory，是更完整的容器
> - 采用**预加载**——启动时就创建所有单例 Bean（饿汉模式）
> - 除了基本 IOC 功能外，还提供：
>   - 国际化（MessageSource）
>   - 事件发布（ApplicationEventPublisher）
>   - 资源加载（ResourceLoader）
>   - 注解处理（@Autowired、@Value 等）
>   - AOP 自动代理（BeanPostProcessor）
>
> **继承体系：**
>
> ```
> BeanFactory（接口）
>     ↓
> ApplicationContext（接口）
>     ↓
>     ├→ ClassPathXmlApplicationContext（XML配置）
>     ├→ FileSystemXmlApplicationContext（XML配置）
>     ├→ AnnotationConfigApplicationContext（注解配置）
>     ├→ FileSystemXmlApplicationContext（Spring Boot CLI）
>     └→ AnnotationConfigServletWebServerApplicationContext（Web环境）
> ```
>
> **实际选择：**
>
> 企业级应用几乎全部使用 ApplicationContext。BeanFactory 几乎只在 Spring 测试框架和低资源嵌入式场景下使用。
>
> **面试加分点：** Spring Boot 默认使用的是 AnnotationConfigServletWebServerApplicationContext，它是 ApplicationContext 的一种实现，启动时会自动配置嵌入式 Web 服务器。

---

## Q5：Bean 的作用域（scope）

### 面试口述版

> Spring 为 Bean 提供了 5 种作用域，通过 @Scope 注解指定。
>
> **五种作用域：**
>
> | 作用域 | 说明 | 使用场景 |
> |-------|------|---------|
> | **singleton**（默认） | 整个容器中只有一个实例 | 绝大多数 Bean（Service、DAO、Config） |
> | **prototype** | 每次请求都创建一个新实例 | 有状态的 Bean，状态不可共享 |
> | **request** | 每次 HTTP 请求创建一个新实例（仅 Web 环境） | HTTP 请求相关的 Bean |
> | **session** | 同一个 HTTP Session 创建一个实例（仅 Web 环境） | 用户会话相关数据 |
> | **application** | 整个 ServletContext 生命周期内只有一个实例 | 全局共享的应用级 Bean |
>
> ** singleton 是默认作用域的原因：**
>
> - 大多数 Bean 是无状态的（Service、DAO、Config），可以安全地全局共享
> - Spring 的设计哲学认为：除非明确需要，否则不应创建多个实例
> - 单例减少了对象创建销毁的开销，性能最优
>
> **prototype 作用域的坑：**
>
> - Spring 不会管理 prototype Bean 的完整生命周期——容器只负责创建，不负责销毁
> - 调用者需要自行管理 prototype Bean 的清理，否则可能导致内存泄漏
> - 如果 prototype Bean 持有数据库连接、线程等资源，不要用 prototype，应该手动管理
>
> **面试加分点：** 在 Spring MVC 中，request 和 session 作用域的 Bean 会通过 RequestContextListener 或 RequestContextFilter 绑定到当前线程，请求/会话结束后自动清理。

---

## Q6：Spring Bean 生命周期（14 个步骤）

### 面试口述版

> Spring Bean 的生命周期是一个高频面试题，我把完整流程梳理成 14 个步骤。
>
> **实例化阶段（第 1 步）：**
>
> 1. **Bean 实例化**：通过反射调用构造函数创建 Bean 实例
>
> **属性填充阶段（第 2 步）：**
>
> 2. **依赖注入**：通过构造器参数、Setter 方法或字段注入标记了 @Autowired / @Resource 的依赖
>
> **初始化阶段（第 3-8 步）：**
>
> 3. **Aware 接口回调**：如果 Bean 实现了 BeanNameAware、BeanFactoryAware 或 ApplicationContextAware，Spring 会注入相应的容器资源
>
> 4. **BeanPostProcessor 前置处理**：容器中所有 BeanPostProcessor 的 postProcessBeforeInitialization() 被调用。**这里常用于扩展**，比如 Spring 的 @Autowired 注解就是通过 AutowiredAnnotationBeanPostProcessor 在这一步处理的
>
> 5. **@PostConstruct 方法**：如果 Bean 有标注 @PostConstruct 的方法，在这一步执行
>
> 6. **InitializingBean 接口**：如果 Bean 实现了 InitializingBean 接口，调用 afterPropertiesSet()
>
> 7. **init-method**：如果 Bean 指定了 init-method，在这一步调用
>
> 8. **BeanPostProcessor 后置处理**：容器中所有 BeanPostProcessor 的 postProcessAfterInitialization() 被调用。**AOP 代理对象就是在这里生成的**——原始 Bean 在这里被替换成代理对象
>
> **使用阶段（第 9 步）：**
>
> 9. **Bean 就绪**：Bean 正常使用
>
> **销毁阶段（第 10-14 步）：**
>
> 10. **@PreDestroy 方法**：如果 Bean 有标注 @PreDestroy 的方法，在容器关闭前执行
>
> 11. **DisposableBean 接口**：如果 Bean 实现了 DisposableBean 接口，调用 destroy()
>
> 12. **destroy-method**：如果 Bean 指定了 destroy-method，在这一步调用
>
> **核心扩展点总结（面试高频追问）：**
>
> - **BeanPostProcessor.postProcessBeforeInitialization()**：@Autowired、@Value、@Inject 等注解的处理
> - **BeanPostProcessor.postProcessAfterInitialization()**：AOP 代理对象的生成
> - **Aware 接口**：让 Bean 感知自己被 Spring 容器管理
> - **@PostConstruct / @PreDestroy**：取代 XML 中的 init-method/destroy-method，更简洁
>
> **面试加分点：** Spring 中最常用的两个扩展点就是 BeanPostProcessor 的前后两个方法——前处理做注解注入，后处理做 AOP 代理。理解这两个扩展点，就理解了 Spring 的核心扩展机制。

---

## Q7：Spring 常用注解对比

### 面试口述版

> 这一题考察的是对 Spring 注解体系的整体理解，我从组件注册、依赖注入、配置三个维度来讲。
>
> **组件注册注解（@Component 系）：**
>
> | 注解 | 层级 | 用途 | 特殊处理 |
> |------|------|------|---------|
> | @Component | 通用 | 通用组件 | 无 |
> | @Service | 业务层 | 业务服务类 | 无（与 @Component 等价，仅语义区分） |
> | @Repository | 持久层 | DAO 类 | 有，Spring 会自动将 SQL 异常转换为 DataAccessException |
> | @Controller | 控制层 | MVC 控制器 | 有，配合 @RequestMapping 使用 |
> | @Configuration | 配置层 | 配置类 | 有，@Configuration 本身是 @Component，但支持 @Bean 声明式创建 Bean |
>
> **@Component 和 @Bean 的区别：**
>
> - @Component 作用于类，Spring 扫描到类时自动创建 Bean
> - @Bean 作用于方法，显式声明 Bean 的创建方式，通常用于引入第三方库（无法加 @Component 注解）
>
> **依赖注入注解（@Autowired vs @Resource）：**
>
> | 对比维度 | @Autowired | @Resource |
> |---------|-----------|-----------|
> | 来源 | Spring 框架 | JDK 标准注解（JSR-250） |
> | 默认方式 | **按类型**（Type）注入 | **按名称**（Name）注入，找不到再按类型 |
> | 匹配顺序 | 类型 → @Qualifier → 字段名 | 名称 → 类型 |
> | Required 属性 | 支持 @Autowired(required=false) | 不支持 |
>
> **@Autowired + @Qualifier = @Resource(name="xxx")**——当一个接口有多个实现类时，两者配合使用可以精确指定注入哪个 Bean。
>
> **面试加分点：** 可以提到 @Repository 的特殊处理——Spring Data Access 异常转换机制。当 DAO 层抛出 SQLException 时，Spring 会自动将其包装成 Spring 的 DataAccessException 体系，这是 Spring 统一异常处理的一部分。

---

---

# 第二部分：Spring AOP 原理

---

## Q8：AOP 核心概念（切面/切点/通知/连接点/织入）

### 面试口述版

> AOP（Aspect Oriented Programming，面向切面编程）是 Spring 的核心特性之一，解决的是"横切关注点"问题。
>
> **什么是横切关注点？**
>
> 比如日志记录、事务管理、安全校验、缓存处理——这些功能散落在业务代码的各个角落，在每个类里重复写，叫"代码侵入性"。AOP 的思想是把这些横切逻辑抽离出来，单独管理，按需织入。
>
> **AOP 核心概念：**
>
> | 概念 | 说明 | 类比 |
> |------|------|------|
> | **Aspect（切面）** | 横切关注点的模块化封装，包含通知和切点 | 一把瑞士军刀，包含多个工具 |
> | **Join Point（连接点）** | 程序执行的某个可插入的点 | 可以插队的"时机" |
> | **Pointcut（切点）** | 实际选择哪些连接点的表达式 | "在哪些时机插队"的规则 |
> | **Advice（通知）** | 切面在连接点执行的具体动作（@Before/@After/@Around 等） | 具体要做什么动作 |
> | **Weaving（织入）** | 把切面应用到目标对象的过程 | 把瑞士军刀装配到生产线上 |
>
> **Join Point vs Pointcut：**
>
> - Join Point 是"所有可以插入的点"（Spring AOP 中只有方法执行这一个 Join Point）
> - Pointcut 是"实际选择了哪些点"，是 Join Point 的子集
>
> **织入时机（Weaving Timing）：**
>
> - **编译时织入**（AspectJ compiler）：编译时直接修改 .class 文件，性能最好但需要特殊编译器
> - **类加载时织入**（AspectJ agent）：Java 5 的类加载器机制
> - **运行时织入**（Spring AOP）：运行时通过代理实现，侵入性最低但有性能开销
>
> **面试加分点：** Spring AOP 只支持方法级别的 Join Point（只能拦截方法调用）。如果需要拦截构造器调用、属性访问等更细粒度的切面，需要使用 AspectJ。

---

## Q9：JDK 动态代理 vs CGLIB 的区别与选择策略

### 面试口述版

> 这是 AOP 最常被追问的题目，必须掌握。
>
> **JDK 动态代理：**
>
> - 基于 `java.lang.reflect.Proxy` 和 `InvocationHandler` 接口
> - **目标类必须实现至少一个接口**
> - 运行时生成代理类字节码，实现目标类实现的所有接口
> - 方法调用通过反射 `invoke()` 执行，有额外开销
>
> **CGLIB（Code Generation Library）：**
>
> - 基于字节码操作库（ASM），生成目标类的**子类**
> - **不需要目标类实现接口**，通过继承实现
> - 方法调用通过 `MethodInterceptor.intercept()` 执行
> - 无法代理 final 类和 final 方法（因为无法被子类化）
>
> **Spring 的选择策略（DefaultAopProxyFactory）：**
>
> ```
> 目标类实现了接口吗？
>     ├→ 是 → JDK 动态代理（默认）
>     └→ 否 → CGLIB 代理
>
> 但有以下三种情况会强制使用 CGLIB：
>     ├→ optimize = true
>     ├→ proxyTargetClass = true（推荐，@EnableAspectJAutoProxy 默认开启）
>     └→ 目标类没有实现接口
> ```
>
> **Spring Boot 2.x 的变化：**
>
> Spring Boot 2.x 开始，默认使用 CGLIB 代理（proxyTargetClass = true），不再遵循"实现接口用 JDK"的默认策略。这样做的好处是：无论目标类是否实现接口，代理对象都和目标对象类型相同，避免了类型转换的问题。
>
> **JDK 动态代理 vs CGLIB 性能对比（2025 年 Spring 6.x 数据）：**
>
> - JDK 动态代理：创建时间约 0.12ms，方法调用延迟约 35ns
> - CGLIB：创建时间约 0.15ms（略慢），方法调用延迟约 20ns（更快）
>
> CGLIB 的方法调用性能更好，因为是直接调用字节码生成的子类方法，而 JDK 代理是反射调用。
>
> **面试加分点：** 可以提到 Spring 6.x（Spring Boot 3.x 基于 Spring 6.x）的优化——引入了代理对象缓存机制，缓存命中后创建耗时从 15ms 降至 0.3ms。

---

## Q10：Spring AOP 源码流程（ProxyFactory / Advisor / MethodInterceptor）

### 面试口述版

> Spring AOP 的源码流程是高频深挖题，我来梳理核心流程。
>
> **核心流程分三步：**
>
> **第一步：创建 ProxyFactory（代理工厂）。**
>
> ProxyFactory 是 Spring AOP 的核心类，负责创建代理对象。它封装了目标对象和切面信息，根据配置决定使用 JDK 代理还是 CGLIB。
>
> **第二步：获取增强器（Advisor）。**
>
> Spring 从容器中获取所有实现了 Advisor 接口的 Bean。Advisor 包含了 Pointcut（切点表达式）和 Advice（通知）。
>
> - **Pointcut 匹配方法：** 通过 Pointcut 检查当前方法是否匹配切点表达式
> - **Advisor 适配为 MethodInterceptor 链：** 匹配成功后，Spring 将 Advisor 适配成 MethodInterceptor 链
>
> **第三步：创建代理对象并执行。**
>
> ```
> 客户端调用
>     ↓
> 代理对象（JDK Proxy 或 CGLIB Subclass）
>     ↓
> 调用 invoke() / intercept()
>     ↓
> 获取匹配的 MethodInterceptor 链
>     ↓
> 逐个执行 MethodInterceptor 的 proceed()
>     ↓
> 执行完所有拦截器后，调用 target.invokeJoinpoint() 执行原始方法
> ```
>
> **关键源码位置：**
>
> - `JdkDynamicAopProxy.invoke()`：JDK 动态代理的方法调用入口
> - `ObjenesisCglibAopProxy.intercept()`：CGLIB 代理的方法调用入口
> - `ReflectiveMethodInvocation.proceed()`：执行 MethodInterceptor 链的核心逻辑
>
> **面试加分点：** 可以提到 MethodInterceptor 链的递归调用机制——`proceed()` 方法内部递归调用自身，每次调用一个拦截器，下一个拦截器的 `proceed()` 调用触发下一个拦截器，直到全部执行完才执行目标方法。这种递归模式确保了通知的顺序执行。

---

## Q11：五种通知的执行顺序

### 面试口述版

> Spring AOP 的五种通知，面试中经常要求说出执行顺序。
>
> **五种通知的执行顺序：**
>
> **@Around（环绕通知）：完全控制目标方法的执行**
>
> - 最强通知，必须显式调用 `proceed()` 才执行目标方法
> - 可以在 `proceed()` 前后穿插任意逻辑
>
> **@Before（前置通知）：在目标方法执行之前运行**
>
> - 执行时机：在 `proceed()` 调用之前
> - 如果 @Around 不调用 `proceed()`，@Before 不会执行
>
> **@After（后置通知）：在目标方法执行之后运行（无论是否抛出异常）**
>
> - 等同于 try-catch 的 finally 块，finally 中的代码无论是否抛异常都会执行
> - 在 @AfterReturning 和 @AfterThrowing 之前执行
>
> **@AfterReturning（返回通知）：在目标方法正常返回后运行**
>
> - 只有目标方法正常返回（非异常）时才执行
> - 可以通过 `returning` 属性获取目标方法的返回值
>
> **@AfterThrowing（异常通知）：在目标方法抛出异常后运行**
>
> - 只有目标方法抛出指定类型的异常时才执行
> - 可以通过 `throwing` 属性获取抛出的异常对象
>
> **执行顺序总结：**
>
> ```
> @Around 前置部分
>     ↓
> @Before
>     ↓
> try {
>     目标方法执行
> } catch {
>     @AfterThrowing（捕获异常）
> } finally {
>     @After（始终执行）
> }
> @Around 后置部分
>     ↓
> @AfterReturning（正常返回）
> ```
>
> **面试加分点：** 如果 @Around 不调用 `proceed()`，目标方法就不会执行，后续所有通知都不会执行。如果 @Around 捕获了异常但不重新抛出，@AfterThrowing 不会执行。

---

---

# 第三部分：Spring 循环依赖与三级缓存

---

## Q12：循环依赖的本质与三种类型

### 面试口述版

> **循环依赖是指两个或多个组件相互引用形成的闭环关系。**
>
> 典型的场景：
>
> - A 依赖 B，B 依赖 A
> - A 依赖 B，B 依赖 C，C 依赖 A（三方循环依赖）
>
> **Spring 能解决的循环依赖类型：**
>
> | 类型 | 说明 | 能否解决 |
> |------|------|---------|
> | **Setter 循环依赖（A→B→A）** | 两个 Bean 通过 setter 方法相互注入 | ✅ 可以（三级缓存机制） |
> | **构造器循环依赖 | 一个或两个 Bean 通过构造器注入形成闭环 | ❌ 无法解决 |
> | **prototype 循环依赖** | prototype 作用域的 Bean 之间的循环依赖 | ❌ 无法解决（Spring 不缓存 prototype Bean） |
>
> **Setter 循环依赖可以解决的原因：**
>
> Spring 在 Bean 实例化后（构造函数执行完）、属性填充前，就将"半成品"的 Bean 引用暴露给其他 Bean 使用。这个"提前暴露引用"的机制打破了循环。
>
> **面试加分点：** 可以提到 Spring 默认只支持 singleton Bean 的循环依赖解决，因为 prototype Bean 每次请求都创建新实例，Spring 不缓存它们，无法提前暴露引用。

---

## Q13：三级缓存原理

### 面试口述版

> 三级缓存是 Spring 循环依赖解决的核心机制，用三个 Map 实现。
>
> **三级缓存结构：**
>
> | 缓存 | 存储内容 | 特点 |
> |------|---------|------|
> | **一级缓存（singletonObjects）** | 完全初始化好的 Bean（成品） | 可以直接使用 |
> | **二级缓存（earlySingletonObjects）** | 已实例化但未初始化的 Bean（半成品） | 防止重复创建早期引用 |
> | **三级缓存（singletonFactories）** | ObjectFactory 对象工厂 | 延迟创建早期引用 |
>
> **三级缓存的工作流程（A→B→A 场景）：**
>
> ```
> 步骤1：创建A
>     ├→ 调用构造函数实例化 A
>     ├→ A 的 ObjectFactory 存入三级缓存（singletonFactories）
>     └→ 开始填充 A 的属性
>
> 步骤2：发现A依赖B，开始创建B
>     ├→ 调用构造函数实例化 B
>     ├→ B 的 ObjectFactory 存入三级缓存
>     └→ 开始填充 B 的属性
>
> 步骤3：B发现依赖A
>     ├→ 从三级缓存获取A的ObjectFactory，调用getObject()
>     ├→ 执行SmartInstantiationAwareBeanPostProcessor.getEarlyBeanReference()
>     ├→ 生成A的早期引用（可能是代理对象）
>     ├→ 早期引用存入二级缓存，从三级缓存移除
>     └→ B拿到A的引用，完成属性填充和初始化，进入一级缓存
>
> 步骤4：A拿到完成初始化的B
>     ├→ A完成属性填充
>     ├→ A完成初始化，进入一级缓存（替换掉二级缓存中的早期引用）
>     └→ 循环依赖解决！
> ```
>
> **面试加分点：** 三级缓存并不是为了"提高效率"，而是为了解决 AOP 代理和循环依赖的冲突。如果不需要 AOP，只用一级+三级也能解决基本循环依赖。

---

## Q14⭐：为什么需要三级缓存而不是二级？AOP 冲突如何解决？

### 面试口述版

> 这是循环依赖最核心的追问，必须能说清楚。
>
> **只用一级+二级缓存的问题：**
>
> 假设只有一级（成品）和二级（半成品），三级缓存用 ObjectFactory 直接替代。
>
> 如果 Bean 需要 AOP 代理，**每次从 ObjectFactory 获取都会生成新的代理对象**——第一次获取生成代理A1，第二次获取生成代理A2，第三次获取生成代理A3。同一个 Bean 在内存中有多个代理版本，Bean B 持有的代理 A 和最终容器中的代理 A 不是同一个对象。
>
> **具体场景演示：**
>
> - 假设 A 需要 AOP 代理（加了事务 @Transactional）
> - B 注入 A 时，获取到的是代理 A1
> - C 也注入 A 时，获取到的是代理 A2
> - 但容器最终存的是代理 A3
> - B 和 C 持有的 A 不是最终容器里的 A，事务不生效！
>
> **三级缓存如何解决：**
>
> 二级缓存（earlySingletonObjects）的核心作用是**缓存首次调用 ObjectFactory.getObject() 的结果**。
>
> - 第一次从三级缓存获取 A → 执行 ObjectFactory.getObject()（生成代理）→ 存入二级缓存
> - 后续所有 Bean 获取 A 时 → 从**二级缓存**获取（不会重复生成）
> - 最终 A 初始化完成后 → 从二级缓存移到一级缓存
>
> **为什么不能只用二级？**
>
> 如果只有二级缓存（半成品）和一级缓存（成品），在 A 实例化后（但未初始化），需要把 A 的引用暴露给 B 使用。此时 A 还是原始对象（没有 AOP 代理），如果后续 A 生成了 AOP 代理，B 持有的 A 就不是代理对象。
>
> **三级缓存的设计哲学：**
>
> 三级缓存用 ObjectFactory 延迟决策——**只有在真正发生循环依赖时**（从三级获取），才调用 getEarlyBeanReference() 生成代理对象。如果从未发生循环依赖（大多数 Bean），ObjectFactory 就不会被调用，Bean 按正常流程初始化，没有额外开销。
>
> **面试加分点：** 这体现了 Spring 设计的一个核心原则——"不要提前做不必要的工作"（Lazy Initialization）。三级缓存是效率和正确性之间的最佳平衡。

---

## Q15⭐：构造器循环依赖为什么无法解决？

### 面试口述版

> 构造器循环依赖无法通过缓存机制解决，这是 Spring 的明确声明。
>
> **根本原因：实例化和初始化是分离的，但构造器注入无法分离。**
>
> **Setter 循环依赖可以解决的原因：**
>
> - Spring 在**构造函数执行后**、**属性填充前**，就暴露半成品引用
> - Bean B 拿到 A 的半成品引用，可以先完成 B 的初始化
> - 等 A 拿到 B 的完整引用，再完成 A 的初始化
>
> **构造器循环依赖无法解决的原因：**
>
> - 构造器必须在实例化阶段完成，构造函数体内就要拿到所有参数
> - 构造函数执行时，Bean 对象还没有创建完成，根本无法暴露引用
> - A 的构造器需要 B，B 的构造器需要 A，形成死锁——没有谁能在不拿到对方的情况下先完成
>
> **Spring 的处理：**
>
> 如果两个 Bean 都通过构造器注入形成循环依赖，Spring 会直接抛出 `BeanCurrentlyInCreationException` 异常（不是等到运行时，而是启动时就报错）。
>
> **为什么 Spring 能快速检测构造器循环依赖？**
>
> 因为 Spring 在创建 Bean 时会维护一个 `singletonsCurrentlyInCreation` 的 Set，记录正在创建的 Bean。如果创建 A 时发现 A 已在创建集合中，说明发生了构造器循环依赖，立即报错。
>
> **实际项目中的解决方式：**
>
> - 将其中一个 Bean 改为 Setter 注入（推荐）
> - 将两个 Bean 都改为 Setter 注入
> - 使用 `@Lazy` 注解延迟加载——构造器参数加 `@Lazy`，Spring 先注入一个代理对象，等到真正调用方法时才创建真实对象
>
> **面试加分点：** 可以提到 `@Lazy` 注解的原理——注入的不是真实对象，而是一个代理对象（类似 JDK 动态代理或 CGLIB），只有代理对象的方法被调用时，才真正触发目标 Bean 的创建。

---

---

# 第四部分：Spring Boot 核心机制

---

## Q16：Spring Boot 启动流程（10 个关键步骤）

### 面试口述版

> Spring Boot 的启动流程是高频面试题，我把完整流程梳理成 10 步。
>
> **第 1 步：SpringApplication 初始化**
>
> - 推断应用类型（Servlet / Reactive / None）
> - 加载 `spring.factories` 中的 ApplicationContextInitializer 和 ApplicationListener
> - 推断 main 方法所在类作为引导类（Bootstrap Class）
>
> **第 2 步：执行 run() 启动计时**
>
> **第 3 步：初始化 SpringApplicationRunListeners**
>
> 发布 `ApplicationStartingEvent` 事件
>
> **第 4 步：准备 Environment（环境配置）**
>
> - 加载 application.properties / application.yml
> - 加载 OS 环境变量、命令行参数、JVM 系统属性
> - 发布 `ApplicationEnvironmentPreparedEvent` 事件
>
> **第 5 步：打印 Banner（启动横幅）**
>
> - 在控制台打印 Spring Boot 的 ASCII Banner
> - 可以通过 `spring.banner.location` 自定义 Banner
>
> **第 6 步：创建 ApplicationContext（根据 Web 类型）**
>
> - Servlet 环境：`AnnotationConfigServletWebServerApplicationContext`
> - Reactive 环境：`AnnotationConfigReactiveWebServerApplicationContext`
> - 非 Web 环境：`AnnotationConfigApplicationContext`
>
> **第 7 步：准备上下文（PrepareContext）**
>
> - 将 Environment 设置到 ApplicationContext
> - 加载引导类（@SpringBootApplication 标注的类）
> - 发布 `ApplicationContextInitializedEvent`
>
> **第 8 步：refreshContext()（核心步骤）**
>
> - **这是最重要的步骤**，等同于 Spring Framework 的容器刷新
> - 触发 Bean 的扫描、实例化、依赖注入、自动配置
> - 调用所有 BeanFactoryPostProcessor（包括自动配置相关的）
> - 发布 `ApplicationPreparedEvent`
>
> **第 9 步：启动嵌入式 Web 服务器**
>
> - 创建内嵌 Tomcat/Jetty/Undertow
> - 发布 `ServletWebServerInitializedEvent`
>
> **第 10 步：发布 Ready 事件**
>
> - 发布 `ApplicationStartedEvent` 和 `ApplicationReadyEvent`
> - 应用启动完成，随时接收 HTTP 请求
>
> **面试加分点：** refreshContext() 是整个 Spring Boot 启动的核心，所有 Bean 的创建、自动配置、组件扫描都在这一步完成。

---

## Q17⭐：Spring Boot 自动配置原理

### 面试口述版

> 自动配置是 Spring Boot 最核心的特性，也是面试高频深挖题。
>
> **核心注解：@SpringBootApplication**
>
> @SpringBootApplication 是一个组合注解，包含了三个核心注解：
>
> - **@SpringBootConfiguration**：等价于 @Configuration，标记这是一个配置类
> - **@EnableAutoConfiguration**：开启自动配置
> - **@ComponentScan**：组件扫描，默认扫描主类所在的包及其子包
>
> **@EnableAutoConfiguration 的核心：@Import(AutoConfigurationImportSelector.class)**
>
> AutoConfigurationImportSelector 实现了 DeferredImportSelector 接口，它在所有 @Configuration 类处理完后才运行。
>
> **自动配置的三个核心问题：**
>
> **问题一：创建哪些 Bean？**
>
> Spring Boot 通过 `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports` 文件（Spring Boot 2.7+ 新格式）发现自动配置类。Spring Boot 扫描 classpath 下所有这个文件，读取里面的配置类全限定名。
>
> **问题二：Bean 的属性从哪来？**
>
> 通过 `@ConfigurationProperties` 注解将 application.yml 中的配置绑定到 Bean 属性。例如 `server.port` 绑定到 ServerProperties 类的 port 字段。
>
> **问题三：哪些 Bean 应该生效？**
>
> 通过 Conditional 系列注解控制：
>
> | 注解 | 条件 |
> |------|------|
> | @ConditionalOnClass | 当 classpath 中存在指定类时才生效 |
> | @ConditionalOnMissingClass | 当 classpath 中不存在指定类时才生效 |
> | @ConditionalOnBean | 当容器中存在指定 Bean 时才生效 |
> | @ConditionalOnMissingBean | 当容器中不存在指定 Bean 时才生效 |
> | @ConditionalOnProperty | 当配置属性满足条件时才生效 |
> | @ConditionalOnWebApplication | 当是 Web 应用时才生效 |
>
> **以 DataSource 自动配置为例：**
>
> ```java
> @Configuration
> @ConditionalOnClass({ DataSource.class, EmbeddedDatabaseType.class })
> @EnableConfigurationProperties(DataSourceProperties.class)
> public class DataSourceAutoConfiguration {
>
>     @Bean
>     @ConditionalOnMissingBean
>     public DataSource dataSource() {
>         return new HikariDataSource();  // 自动创建数据源
>     }
> }
> ```
>
> 如果 classpath 中有 HikariCP 的类，且用户没有手动配置 DataSource，Spring Boot 自动创建一个 HikariDataSource。
>
> **面试加分点：** 可以提到 Spring Boot 2.7 的重大变化——从 `spring.factories` 迁移到 `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports`，解决了配置类重复和加载顺序问题。

---

## Q18：Spring Boot Starter 原理

### 面试口述版

> Starter 是 Spring Boot 的依赖管理核心，让"引入一个依赖，框架自动搞定一切"成为可能。
>
> **Starter 的本质：**
>
> 一个 Starter 本质上是一个 POM 文件 + 一个自动配置类。
>
> **Starter 的工作原理分三步：**
>
> **第一步：依赖传递。**
>
> ```xml
> <!-- spring-boot-starter-web 的 pom.xml -->
> <dependencies>
>     <dependency>spring-boot-starter</dependency>
>     <dependency>spring-boot-starter-tomcat</dependency>
>     <dependency>spring-boot-starter-json</dependency>
>     <dependency>spring-web</dependency>
>     <dependency>spring-webmvc</dependency>
> </dependencies>
> ```
>
> 开发者只需要引入 `spring-boot-starter-web`，所有 Web 开发所需的依赖自动全部引入。
>
> **第二步：自动配置发现。**
>
> Spring Boot 启动时扫描 `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports`，发现 Starter 声明的自动配置类。
>
> **第三步：条件装配。**
>
> 自动配置类根据 @ConditionalOnClass 等注解决定是否生效。
>
> **面试加分点：** 可以提到自定义 Starter 的方法：
>
> 1. 创建一个 jar 项目，pom.xml 中依赖 spring-boot-autoconfigure
> 2. 创建自动配置类，写好 @Configuration 和 @ConditionalOnClass
> 3. 在 `META-INF/spring/xxx.imports` 中声明配置类
> 4. 创建 Starter POM，引入自动配置项目的依赖
>
> 这样别人只需要引入你的 Starter，就能自动获得所有功能。

---

## Q19：Spring Boot 内嵌 Tomcat 原理

### 面试口述版

> Spring Boot 不需要单独安装 Tomcat，应用本身就是服务器。
>
> **内嵌 Tomcat 的工作流程：**
>
> **第一步：自动配置。**
>
> `EmbeddedWebServerFactoryCustomizerAutoConfiguration` 是负责创建内嵌服务器的自动配置类。
>
> 通过 `@ConditionalOnClass({ Tomcat.class, UpgradeProtocol.class })` 判断是否使用 Tomcat。
>
> **第二步：创建 Tomcat 实例。**
>
> 通过 `TomcatWebServerFactoryCustomizer` 创建 Tomcat 实例，配置端口、线程池、连接器等。
>
> **第三步：启动 Tomcat。**
>
> 在 `refreshContext()` 过程中，通过 `TomcatWebServer` 启动 Tomcat，监听配置的端口（默认 8080）。
>
> **端口配置原理：**
>
> 通过 `@EnableConfigurationProperties(ServerProperties.class)` 绑定 `server.port` 配置，自动应用到 Tomcat。
>
> **面试加分点：** 可以提到，Spring Boot 2.x 默认使用 Tomcat（spring-boot-starter-tomcat），如果想换成 Jetty 或 Undertow，只需要排除 Tomcat 依赖并引入其他即可：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jetty</artifactId>
</dependency>
```

---

---

# 第五部分：Spring 事务

---

## Q20⭐：@Transactional 事务失效的经典场景（8 种）

### 面试口述版

> @Transactional 失效是 Spring 事务最经典的面试题。核心原因是：Spring 事务基于 AOP 代理，代理失效或异常无法被捕获时，事务就不生效。
>
> **第一种：同类自调用（最常见）。**
>
> ```java
> @Service
> public class OrderService {
>
>     public void createOrder() {        // 普通方法
>         this.processPayment();          // this调用，不走代理，事务失效！
>     }
>
>     @Transactional                      // 事务不生效
>     public void processPayment() {
>         // 数据库操作
>     }
> }
> ```
>
> 原因：`this` 绕过了 Spring 代理对象，直接调用目标方法，事务拦截器没有机会执行。
>
> 解决方案：
>
> - **拆分到不同类（推荐）**：把 processPayment() 移到另一个 Service
> - **自我注入**：`@Autowired OrderService orderService`，然后 `orderService.processPayment()`
> - **AopContext.currentProxy()**：`((OrderService) AopContext.currentProxy()).processPayment()`
>
> **第二种：非 public 方法。**
>
> `@Transactional` 只能标注在 public 方法上。标注 private/protected 方法不生效，因为 Spring AOP 获取事务属性时，allowPublicMethodsOnly() 对非 public 方法返回 null。
>
> **第三种：异常被 catch 吞掉。**
>
> ```java
> @Transactional
> public void update() {
>     try {
>         doSomething();
>     } catch (Exception e) {
>         // 异常被吞掉了，事务不回滚！
>     }
> }
> ```
>
> 异常被吞掉后，Spring 事务拦截器感知不到异常，不知道需要回滚。
>
> **第四种：非运行时异常不回滚（默认行为）。**
>
> Spring 默认只对 RuntimeException 和 Error 回滚。如果抛出 `throw new Exception()`，不会触发回滚。
>
> 解决方案：`@Transactional(rollbackFor = Exception.class)`
>
> **第五种：对象不是 Spring 容器管理的。**
>
> 自己 new 出来的对象，Spring 无法为其生成代理，事务自然不生效。
>
> **第六种：多线程。**
>
> 事务是线程绑定的。如果开启新线程执行数据库操作，新线程没有绑定到原有事务上下文中。
>
> **第七种：timeout 超时时间过小。**
>
> 如果 timeout 设置过短，方法还没执行完就超时，事务可能异常终止。
>
> **第八种：数据库不支持事务。**
>
> MySQL 的 MyISAM 引擎不支持事务。必须使用 InnoDB 引擎。
>
> **面试加分点：** 可以总结为三个核心原则——**代理必须生效**（同类自调用、非public方法、非容器管理对象会导致代理失效）、**异常必须抛出**（catch吞掉、checked异常不回滚）、**上下文必须传递**（多线程、数据库不支持事务会导致上下文断裂）。

---

## Q21：事务的七种传播行为

### 面试口述版

> Spring 事务传播行为决定了"一个方法在有事务和无事务时如何决定自己的行为"。
>
> **七种传播行为：**
>
> | 传播行为 | 说明 | 常见场景 |
> |---------|------|---------|
> | **REQUIRED（默认）** | 存在事务则加入，不存在则新建 | 绝大多数业务场景 |
> | **REQUIRES_NEW** | 总是新建事务，挂起已有事务 | 日志记录（需要独立提交，不受主事务回滚影响） |
> | **NESTED** | 存在事务则创建保存点（子事务），否则新建 | 复杂业务（部分需要回滚，部分需要提交） |
> | **SUPPORTS** | 存在事务则加入，不存在则非事务执行 | 查询方法（可在事务中也可独立执行） |
> | **MANDATORY** | 必须在已有事务中，否则抛异常 | 强制要求调用方有事务 |
> | **NOT_SUPPORTED** | 非事务执行，存在事务则挂起 | 需要绕过事务的操作 |
> | **NEVER** | 非事务执行，存在事务则抛异常 | 强制无事务环境 |
>
> **三个核心传播行为详解：**
>
> **REQUIRED（默认）：**
>
> A 调用 B，B 使用 REQUIRED。如果 A 有事务，B 加入 A 的事务（A 和 B 共用一个事务）；如果 A 无事务，B 新建一个事务。
>
> 特点：**要么一起提交，要么一起回滚**。
>
> **REQUIRES_NEW：**
>
> A 调用 B，B 使用 REQUIRES_NEW。B 总是新建事务，A 的事务被挂起。A 和 B 各自有独立的事务，B 独立提交，不受 A 的回滚影响。
>
> **NESTED（嵌套事务）：**
>
> A 调用 B，B 使用 NESTED。如果 A 有事务，B 创建保存点（类似子事务）；如果 A 无事务，B 新建事务。
>
> 关键区别：
>
> - A 异常时：A 和 B 一起回滚
> - B 异常时：A 可以捕获异常，不回滚 A（只回滚 B）
>
> **NESTED vs REQUIRES_NEW 的核心区别：**
>
> - REQUIRES_NEW：内层事务完全独立，父事务异常不影响内层提交
> - NESTED：内层是子事务，父事务异常必然导致内层回滚（子事务不能独立提交）
>
> **面试加分点：** 可以提到 NESTED 使用 JDBC 的保存点（Savepoint）实现，而非真正的子事务。这意味着只有部分数据库（Oracle、SQL Server）支持，MySQL 使用 InnoDB 时 NESTED 的行为类似 REQUIRED（因为 MySQL 不支持 Savepoint 在事务中嵌套）。

---

## Q22：Spring 事务隔离级别

### 面试口述版

> Spring 事务隔离级别和数据库隔离级别是对应的，通过 `@Transactional(isolation = ...)` 配置。
>
> **五种隔离级别：**
>
> | 隔离级别 | 说明 | 并发问题 |
> |---------|------|---------|
> | **DEFAULT**（默认） | 使用数据库默认隔离级别 | 取决于数据库 |
> | **READ_UNCOMMITTED** | 读未提交 | 脏读、不可重复读、幻读都可能发生 |
> | **READ_COMMITTED** | 读已提交（Oracle默认） | 防止脏读 |
> | **REPEATABLE_READ** | 可重复读（MySQL默认） | 防止脏读、不可重复读 |
> | **SERIALIZABLE** | 串行化 | 解决所有并发问题，但性能最差 |
>
> **MySQL 的默认隔离级别：REPEATABLE_READ**，通过 MVCC（多版本并发控制）实现，允许同一事务内多次读取一致的数据快照。
>
> **Oracle 的默认隔离级别：READ_COMMITTED**，通过回滚段实现。
>
> **并发问题速记：**
>
> - **脏读**：读到其他事务未提交的数据
> - **不可重复读**：同一事务中两次读取同一行数据，结果不同（因为被其他事务修改并提交了）
> - **幻读**：同一事务中两次查询，结果集不同（因为其他事务插入了新行）
>
> **面试加分点：** 可以提到 Spring 事务隔离级别是底层数据库能力的抽象，Spring 本身不实现隔离级别，只是将配置传递给 JDBC。

---

---

# 第六部分：Spring MVC 与请求处理

---

## Q23⭐：Spring MVC 九大组件与请求处理流程

### 面试口述版

> Spring MVC 的核心是前端控制器模式，DispatcherServlet 是所有请求的入口。
>
> **Spring MVC 九大组件：**
>
> | 组件 | 作用 |
> |------|------|
> | **MultipartResolver** | 处理文件上传请求，包装成 MultipartHttpServletRequest |
> | **LocaleResolver** | 解析国际化地区信息（i18n） |
> | **ThemeResolver** | 解析视图主题 |
> | **HandlerMapping** | 根据请求路径查找对应的 Handler（Controller 方法） |
> | **HandlerAdapter** | 将 Handler 适配成标准 Servlet 处理方法的形式 |
> | **HandlerExceptionResolver** | 处理 Handler 执行中的异常 |
> | **RequestToViewNameTranslator** | 从请求中推导视图名称（当 Handler 未指定 View 时） |
> | **ViewResolver** | 将逻辑视图名（如 "user/list"）解析为物理视图（如 JSP 路径） |
> | **FlashMapManager** | 管理 FlashMap，用于重定向时传递参数 |
>
> **请求处理核心流程（doDispatch）：**
>
> ```
> HTTP请求
>     ↓
> DispatcherServlet.doDispatch()
>     ↓
> 1. HandlerMapping 根据路径找到 Handler（Controller方法）+ 拦截器链
>     ↓
> 2. HandlerAdapter 执行 Handler（Controller方法）
>     ↓
> 3. Controller 返回 ModelAndView
>     ↓
> 4. HandlerExceptionResolver 处理异常
>     ↓
> 5. ViewResolver 将视图名解析为 View 对象
>     ↓
> 6. View 渲染 Model 数据生成 HTML
>     ↓
> HTTP响应
> ```
>
> **HandlerMapping 的选择策略：**
>
> - `@RequestMapping` → `RequestMappingHandlerMapping`
> - `/user/*` → `SimpleUrlHandlerMapping`
> - `/user.action` → `ControllerBeanNameHandlerMapping`
>
> **面试加分点：** 九大组件都是面向接口定义的，如果容器中没有配置，Spring 会使用 `DispatcherServlet.properties` 中的默认值。这种设计允许开发者灵活替换任意组件。

---

## Q24：@RequestMapping 的工作原理

### 面试口述版

> @RequestMapping 是 Spring MVC 中最核心的注解，它的工作原理分为两步。
>
> **第一步：启动时扫描注册。**
>
> Spring MVC 启动时，`RequestMappingHandlerMapping` 会扫描所有标注了 @RequestMapping 的方法，解析注解中的：
>
> - path / value（URL 路径，如 "/user/list"）
> - method（HTTP 方法，如 GET、POST）
> - params（请求参数条件）
> - headers（请求头条件）
> - consumes / produces（请求/响应媒体类型）
>
> 将每个方法和它的 URL 映射关系存入一个 `Map<RequestMappingInfo, HandlerMethod>` 中。
>
> **第二步：运行时匹配。**
>
> 请求到达时，DispatcherServlet 调用 `HandlerMapping.getHandler(request)`：
>
> - 遍历 Map，根据请求路径、HTTP 方法找匹配的 HandlerMethod
> - 找不到返回 null（Spring MVC 会尝试下一个 HandlerMapping）
> - 找到则返回包含 HandlerMethod 和拦截器链的 HandlerExecutionChain
>
> **@GetMapping / @PostMapping 等衍生注解：**
>
> 这些是 @RequestMapping 的组合注解，本质完全一样：
>
> ```java
> @GetMapping = @RequestMapping(method = RequestMethod.GET)
> @PostMapping = @RequestMapping(method = RequestMethod.POST)
> ```
>
> **面试加分点：** 可以提到 @RequestMapping 的路径可以拼接父级路径：

```java
@RestController
@RequestMapping("/api/users")   // 父级路径
public class UserController {

    @GetMapping("/list")         // 完整路径 = /api/users/list
    public List<User> list() {}

    @PostMapping("/create")      // 完整路径 = /api/users/create
    public User create() {}
}
```

---

---

# 第七部分：Spring 设计模式实战

---

## Q25：策略模式在 Spring 中的应用

### 面试口述版

> **策略模式（Strategy Pattern）：定义一系列算法，将每个算法封装起来，使它们可以互相替换。**
>
> **Spring 中的典型应用一：Spring Resource 接口的实现类。**
>
> Spring 的 Resource 接口代表"资源"，有多种实现类对应不同的资源访问策略：
>
> - `UrlResource`：访问 URL 资源
> - `ClassPathResource`：访问 classpath 资源
> - `FileSystemResource`：访问文件系统资源
> - `ServletContextResource`：访问 Web 应用资源
>
> 开发者只需要注入 Resource 接口，Spring 根据资源路径前缀（classpath:、file:、http:）自动选择合适的实现类。
>
> **Spring 中的典型应用二：EmbeddedDatabaseConfigurer（内嵌数据库策略）。**
>
> - HSQL 策略
> - H2 策略
> - Derby 策略
>
> Spring 根据 classpath 中的数据库驱动类自动选择对应的配置器。
>
> **Spring 中的典型应用三：Environment 变量解析策略。**
>
> Spring 根据变量名格式自动判断来源：系统属性 > 环境变量 > 配置文件。
>
> **实际项目重构案例：支付渠道策略。**
>
> ```java
> // 定义支付策略接口
> public interface PaymentStrategy {
>     void pay(BigDecimal amount);
> }
>
> // 微信支付策略
> @Component
> public class WeChatPayStrategy implements PaymentStrategy {
>     @Override
>     public void pay(BigDecimal amount) { /* 微信支付逻辑 */ }
> }
>
> // 支付宝策略
> @Component
> public class AlipayStrategy implements PaymentStrategy {
>     @Override
>     public void pay(BigDecimal amount) { /* 支付宝支付逻辑 */ }
> }
>
> // 支付工厂（策略选择器）
> @Component
> public class PaymentStrategyFactory {
>
>     @Autowired
>     private Map<String, PaymentStrategy> strategyMap;  // 自动注入所有实现类
>
>     public PaymentStrategy getStrategy(String channel) {
>         PaymentStrategy strategy = strategyMap.get(channel + "Strategy");
>         if (strategy == null) {
>             throw new IllegalArgumentException("不支持的支付渠道: " + channel);
>         }
>         return strategy;
>     }
> }
> ```
>
> 客户端只需要调用 `factory.getStrategy("weChat").pay(amount)`，无需关心具体实现。
>
> **面试加分点：** 策略模式的最佳实践是用 Map 自动注入所有实现——`@Autowired private Map<String, T> map`，Spring 会把接口的所有实现类自动注入，key 是 Bean 名称。这种写法比传统的 if-else 或策略工厂简洁得多。

---

## Q26：工厂模式：BeanFactory 与 FactoryBean

### 面试口述版

> Spring 中有两个"工厂"容易混淆，我分别讲。
>
> **BeanFactory：IOC 容器的底层接口。**
>
> BeanFactory 是最底层的 IOC 容器接口，定义了 getBean() 等核心方法。所有 IOC 容器（包括 ApplicationContext）都实现了 BeanFactory。
>
> ```java
> public interface BeanFactory {
>     Object getBean(String name);
>     <T> T getBean(Class<T> requiredType);
>     <T> T getBean(String name, Object... args);
> }
> ```
>
> **FactoryBean：创建特殊 Bean 的工厂接口。**
>
> FactoryBean 是 Spring 提供的一个扩展接口，用于**创建非 Spring 管理的对象**或**复杂的 Bean 创建逻辑**。
>
> ```java
> public interface FactoryBean<T> {
>     T getObject();              // 返回创建的对象
>     Class<?> getObjectType();   // 返回对象的类型
>     boolean isSingleton();      // 是否单例
> }
> ```
>
> **MyBatis 的典型应用：**
>
> MyBatis 集成 Spring 时，`SqlSessionFactoryBean` 是最著名的 FactoryBean 实现：
>
> ```java
> @Configuration
> public class MyBatisConfig {
>     @Bean
>     public SqlSessionFactory sqlSessionFactory() {
>         SqlSessionFactoryBean factoryBean = new SqlSessionFactoryBean();
>         factoryBean.setDataSource(dataSource);
>         factoryBean.setMapperLocations(...);
>         return factoryBean.getObject();  // 实际返回的是 SqlSessionFactory
>     }
> }
> ```
>
> 开发者调用 `sqlSessionFactory()` 方法时，Spring 实际注入的是 `SqlSessionFactoryBean.getObject()` 的返回值——一个真正的 SqlSessionFactory 对象，而不是 FactoryBean 本身。
>
> **两者的核心区别：**
>
> - BeanFactory：**Spring 容器的底层 API**，管理所有 Bean 的生命周期
> - FactoryBean：**创建特定 Bean 的工厂**，是 Bean 的一种特殊形态
>
> **面试加分点：** Spring 的 `ProxyFactoryBean` 是另一个常用的 FactoryBean——用于手动创建 AOP 代理对象。在 Spring 内部，AOP 代理就是通过 ProxyFactoryBean 创建的。

---

## Q27：模板方法模式：JdbcTemplate / RestTemplate

### 面试口述版

> **模板方法模式（Template Method Pattern）：定义一个操作算法的骨架，将某些步骤延迟到子类中实现。**
>
> Spring 大量使用了模板方法模式——把固定流程固定下来，可变部分留给回调实现。
>
> **JdbcTemplate 的模板方法设计：**
>
> ```java
> // JdbcTemplate 封装了数据库操作的固定流程
> jdbcTemplate.execute("INSERT INTO user VALUES (?)", new PreparedStatementCallback() {
>     @Override
>     public Object doInPreparedStatement(PreparedStatement ps) throws SQLException {
>         ps.setString(1, "张三");  // 只有这部分可变
>         return ps.executeUpdate();
>     }
> });
> ```
>
> 固定流程：获取连接 → 创建语句 → 执行回调 → 关闭语句和连接
>
> 可变部分：SQL 参数设置和结果处理，由回调（PreparedStatementCallback）完成
>
> **Spring 源码中的模板方法：**
>
> - **JdbcTemplate** → `execute()` 方法是模板方法，PreparedStatementCallback 是回调
> - **RestTemplate** → `execute()` 方法是模板方法，ResponseExtractor 是回调
> - **JpaTemplate** → `execute()` 方法是模板方法，JpaCallback 是回调
> - **TransactionTemplate** → `execute()` 方法是模板方法，TransactionCallback 是回调
>
> **与策略模式的区别：**
>
> - **策略模式**：每种策略是完全独立的算法，互相替代
> - **模板方法模式**：有固定的骨架流程，可变的是骨架中的某个步骤
>
> **实际项目重构案例：批量导入的模板方法。**
>
> ```java
> public abstract class AbstractBatchImportTemplate<T> {
>
>     public final void importBatch(List<String> dataList) {
>         List<T> validList = new ArrayList<>();
>         for (String data : dataList) {
>             try {
>                 T item = parse(data);          // 子类实现：解析
>                 if (validate(item)) {           // 固定：验证
>                     validList.add(item);
>                 }
>             } catch (Exception e) {
>                 handleError(data, e);            // 固定：错误处理
>             }
>         }
>         saveAll(validList);                     // 子类实现：保存
>     }
>
>     protected abstract T parse(String data);    // 子类实现
>     protected abstract void saveAll(List<T> items);  // 子类实现
>     protected boolean validate(T item) { return true; }  // 可选覆盖
>     protected void handleError(String data, Exception e) { /* 日志记录 */ }
> }
>
> // 具体实现：用户批量导入
> @Component
> public class UserBatchImportTemplate extends AbstractBatchImportTemplate<User> {
>     @Override
>     protected User parse(String data) { /* CSV解析 */ }
>     @Override
>     protected void saveAll(List<User> items) { userDao.batchInsert(items); }
>     @Override
>     protected boolean validate(User user) { return user.getAge() > 0; }
> }
> ```
>
> **面试加分点：** 模板方法模式的"钩子方法"（Hook Method）是精髓——像 `validate()` 那样有默认实现的子类可覆盖也可不覆盖的方法。Spring 的 `JdbcTemplate.execute()` 就是通过回调接口实现了完全的扩展性。

---

## Q28：单例模式：Spring Bean 默认单例的原因

### 面试口述版

> Spring Bean 默认是单例的，这是 Spring 的核心设计决策之一。
>
> **为什么 Spring 默认单例？**
>
> **第一，性能最优。** Bean 创建和销毁有成本。单例模式下，每个 Bean 只创建一次，高频使用时避免了重复创建的性能损耗。
>
> **第二，减少内存占用。** 如果每次注入都创建新实例，内存中会存在大量相同类型的对象。
>
> **第三，设计哲学。** Spring 团队认为：除非 Bean 有明确的状态（prototype），否则都应该是无状态、可共享的。单例模式简化了依赖管理的复杂度。
>
> **单例 Bean 的线程安全问题：**
>
> 单例 Bean 如果有可变状态（成员变量），在高并发下会出现线程安全问题。
>
> **解决方案：**
>
> - **方案一：无状态设计。** Bean 只处理数据，不存储状态。Service、DAO 都应该是无状态的。
> - **方案二：ThreadLocal。** 如果 Bean 需要存储线程相关状态，用 ThreadLocal。
> - **方案三：prototype。** 如果必须存储可变状态，改用 prototype，每次注入创建新实例。
>
> **Spring 中的单例注册表：**
>
> Spring 使用 `DefaultSingletonBeanRegistry` 管理单例 Bean：
>
> ```java
> public class DefaultSingletonBeanRegistry {
>     private final Map<String, Object> singletonObjects = new ConcurrentHashMap<>();
>
>     public Object getSingleton(String beanName) {
>         return singletonObjects.get(beanName);
>     }
>
>     protected void addSingleton(String beanName, Object singletonObject) {
>         synchronized (this.singletonObjects) {
>             this.singletonObjects.put(beanName, singletonObject);
>         }
>     }
> }
> ```
>
> **面试加分点：** 可以提到 Spring 的单例不是"饿汉/懒汉"的简单选择——Spring 容器级的单例（通过 singletonObjects Map 管理），和有状态的类（存在成员变量）结合时需要特别注意线程安全。

---

## Q29：观察者模式：ApplicationEvent 与 ApplicationListener

### 面试口述版

> **观察者模式（Observer Pattern）：定义对象间一对多的依赖关系，当一个对象状态变化时，所有依赖它的对象自动收到通知。**
>
> Spring 的事件机制是观察者模式的典型实现。
>
> **核心组件：**
>
> - **ApplicationEvent**：事件对象，所有自定义事件需要继承它
> - **ApplicationEventPublisher**：事件发布者，负责发布事件
> - **ApplicationListener**：事件监听器，监听并处理事件
>
> **使用示例：**
>
> ```java
> // 1. 定义事件
> public class OrderCreatedEvent extends ApplicationEvent {
>     private final Order order;
>     public OrderCreatedEvent(Object source, Order order) {
>         super(source);
>         this.order = order;
>     }
> }
>
> // 2. 定义监听器
> @Component
> public class OrderNotifyListener implements ApplicationListener<OrderCreatedEvent> {
>     @Override
>     public void onApplicationEvent(OrderCreatedEvent event) {
>         Order order = event.getOrder();
>         sendNotify(order);  // 发送通知
>     }
> }
>
> // 3. 发布事件
> @Service
> public class OrderService {
>     @Autowired
>     private ApplicationEventPublisher publisher;
>
>     public void createOrder(Order order) {
>         orderDao.save(order);
>         publisher.publishEvent(new OrderCreatedEvent(this, order));  // 发布事件
>     }
> }
> ```
>
> **@EventListener 注解（简化写法）：**
>
> ```java
> @Component
> public class OrderNotifyListener {
>     @EventListener
>     public void handleOrderCreated(OrderCreatedEvent event) {
>         sendNotify(event.getOrder());
>     }
>
>     @Async  // 可以配合异步执行
>     @EventListener
>     public void handleOrderCreatedAsync(OrderCreatedEvent event) {
>         // 异步发送通知，不阻塞主流程
>     }
> }
> ```
>
> **Spring Boot 的事件驱动机制：**
>
> Spring Boot 的启动流程就是通过 ApplicationEvent 驱动的：
>
> - `ApplicationStartingEvent`：启动中
> - `ApplicationEnvironmentPreparedEvent`：环境准备好
> - `ApplicationPreparedEvent`：容器准备好
> - `ApplicationStartedEvent`：已启动
> - `ApplicationReadyEvent`：已就绪
>
> **事务同步器（TransactionSynchronization）：**
>
> Spring 事务也使用了观察者模式——通过 `TransactionSynchronizationManager.registerSynchronization()` 注册事务同步器，在事务提交后或回滚后自动执行回调：
>
> ```java
> TransactionSynchronizationManager.registerSynchronization(
>     new TransactionSynchronization() {
>         @Override
>         public void afterCommit() {
>             // 事务提交后执行，比如发送消息（此时事务已提交，消息不会丢失）
>             messageQueue.send(order.toJson());
>         }
>     }
> );
> ```
>
> **面试加分点：** 可以提到，事务同步器是"在事务提交后执行"的标准做法。比直接发送消息更安全——如果消息发送在事务提交前，事务回滚了消息却发出去了，会造成数据不一致。

---

## Q30：AOP 代理模式：JdkDynamicAopProxy / ObjenesisCglibAopProxy

### 面试口述版

> **代理模式（Proxy Pattern）：为其他对象提供一个替身或占位符，以控制对这个对象的访问。**
>
> Spring AOP 底层就是代理模式的具体应用。
>
> **JdkDynamicAopProxy（JDK 动态代理）：**
>
> ```java
> public class JdkDynamicAopProxy implements AopProxy, InvocationHandler {
>
>     @Override
>     public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
>         // 获取适用于当前方法的增强器链
>         List<Object> chain = this.advised.getInterceptorsAndDynamicInterceptionMatchers(
>             method, targetClass);
>
>         // 执行增强器链
>         ReflectiveMethodInvocation invocation = new ReflectiveMethodInvocation(
>             proxy, target, method, args, targetClass, chain);
>         return invocation.proceed();
>     }
> }
> ```
>
> **ObjenesisCglibAopProxy（CGLIB 代理）：**
>
> ```java
> public class ObjenesisCglibAopProxy extends CglibAopProxy {
>
>     @Override
>     public Object intercept(Object proxy, Method method, Object[] args, MethodProxy methodProxy) throws Throwable {
>         // 获取适用于当前方法的增强器链
>         List<Object> chain = this.advised.getInterceptorsAndDynamicInterceptionMatchers(
>             method, targetClass);
>
>         // 执行增强器链
>         ReflectiveMethodInvocation invocation = new ReflectiveMethodInvocation(
>             proxy, target, method, args, targetClass, chain);
>         return invocation.proceed();
>     }
> }
> ```
>
> **两者的共同点：**
>
> - 最终都调用 `ReflectiveMethodInvocation.proceed()` 执行增强器链
> - 增强器链的执行顺序完全一致
> - 最终都调用 `method.invoke(target, args)` 执行原始方法
>
> **代理模式的实际业务应用：**
>
> - **延迟加载代理**：Hibernate 的延迟加载通过代理实现——真正访问关联对象时才发起 SQL
> - **数据访问代理**：读写分离——自动将写操作路由到主库，读操作路由到从库
> - **安全代理**：在方法执行前检查用户权限
>
> **面试加分点：** 可以提到，Spring AOP 的代理模式和装饰器模式（Decorator Pattern）非常相似——两者都是创建一个包装对象，在原始对象前后添加逻辑。区别在于：代理模式中客户端不知道真实对象的存在（对用户透明），装饰器模式中用户知道被装饰的对象。

---

## Q31：装饰器模式：HttpServletRequestWrapper / ResponseWrapper

### 面试口述版

> **装饰器模式（Decorator Pattern）：动态地给对象添加额外职责，比继承更灵活。**
>
> Spring MVC 中的请求和响应包装器是装饰器模式的典型应用。
>
> **HttpServletRequestWrapper：**
>
> ```java
> // 需求：统计接口耗时
> public class TimingRequestWrapper extends HttpServletRequestWrapper {
>
>     public TimingRequestWrapper(HttpServletRequest request) {
>         super(request);
>     }
>
>     @Override
>     public Object getAttribute(String name) {
>         long start = System.currentTimeMillis();
>         Object result = super.getAttribute(name);  // 包装获取逻辑
>         System.out.println("getAttribute 耗时: " + (System.currentTimeMillis() - start) + "ms");
>         return result;
>     }
> }
> ```
>
> **使用 Filter 注册包装器：**
>
> ```java
> @Component
> public class TimingFilter implements Filter {
>
>     @Override
>     public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
>             throws IOException, ServletException {
>         chain.doFilter(
>             new TimingRequestWrapper((HttpServletRequest) request),
>             response
>         );
>     }
> }
> ```
>
> **装饰器模式的常见业务应用：**
>
> - **请求参数增强**：在包装器中自动注入当前登录用户
> - **响应数据加密**：在包装器中对响应体加密
> - **响应数据格式化**：在包装器中统一包装响应结构 `{code, message, data}`
>
> **装饰器模式 vs 代理模式：**
>
> - **装饰器模式**：动态组合多个装饰器，可以任意叠加装饰器，运行时决定
> - **代理模式**：编译时决定代理关系，代理是固定的
>
> 在 Spring MVC 中，装饰器模式让 Filter 在不修改原有 Filter 的情况下，为请求和响应增加额外功能。
>
> **面试加分点：** 可以提到 Java I/O 类库大量使用了装饰器模式——`InputStream` 是抽象组件，`FileInputStream` / `BufferedInputStream` / `GZIPInputStream` 等是具体装饰器，可以层层嵌套叠加（`new BufferedInputStream(new GZIPInputStream(new FileInputStream("file.gz")))`）。

---

---

# 第八部分：Filter / Interceptor / AOP 对比

---

## Q32：Filter / Interceptor / AOP 执行顺序与区别

### 面试口述版

> 这是道经典对比题，展示对 Spring 请求处理全链路的理解。
>
> **执行顺序：**
>
> ```
> 请求到达
>     ↓
> Filter（Servlet 规范，最外层）
>     ↓
> Interceptor（Spring MVC，中间层）
>     ↓
> AOP（Spring 容器，最细粒度）
>     ↓
> Controller
>     ↓
> AOP（后置处理）
>     ↓
> Interceptor（后置/完成后）
>     ↓
> Filter（返回时）
>     ↓
> 响应返回
> ```
>
> **Filter（过滤器）：**
>
> - 来源：Servlet 规范，javax.servlet.Filter，与 Spring 无关
> - 执行时机：请求到达 Servlet 前和响应返回前
> - 粒度：最外层，最粗粒度，**无法获取 Controller 和方法信息**
> - 无法注入 Spring Bean（Filter 初始化在 Spring 容器之前）
> - 场景：全局鉴权、字符编码、接口耗时统计、危险字符过滤
>
> **Interceptor（拦截器）：**
>
> - 来源：Spring MVC 原生功能，依赖 Spring MVC
> - 执行时机：请求到达 Controller 前后
> - 粒度：中等粒度，**能获取 Controller 对象和方法名，但无法获取方法参数**
> - 可以注入 Spring Bean（Spring 容器已初始化）
> - 三个方法：preHandle（返回 false 则短路，后续都不执行）、postHandle、afterCompletion（无论异常都执行）
> - 场景：接口权限验证、请求头处理、统一错误处理
>
> **AOP（切面）：**
>
> - 来源：Spring IOC 容器的扩展机制
> - 执行时机：方法调用前/后/异常时
> - 粒度：最细粒度，**能获取方法参数和返回值**
> - 只能拦截 Spring 容器管理的 Bean
> - 无法获取 Request/Response 对象
> - 场景：服务层方法缓存、事务管理、日志记录、参数安全验证
>
> **选择原则：**
>
> - **能用 Filter 的不用 Interceptor**（Filter 范围更广）
> - **能用 Interceptor 的不用 AOP**（AOP 只能拦截 Bean）
> - 选择**距离目标最近**的方式，避免无谓的跨层操作
>
> **面试加分点：** 可以提到 Filter 的注册方式——传统方式通过 web.xml 配置，Spring Boot 中通过 `@WebFilter` 注解或 `FilterRegistrationBean` 编程式注册。使用 `FilterRegistrationBean` 可以控制 Filter 的执行顺序。

---

---

# 追问补充篇：各知识点高频追问

---

## 追问 1：BeanPostProcessor 在 Spring 中有哪些实际应用？

### 追问回答

> BeanPostProcessor 是 Spring 最重要的扩展点，实际应用非常广泛。
>
> **Spring 内置的 BeanPostProcessor：**
>
> - **AutowiredAnnotationBeanPostProcessor**：处理 @Autowired、@Value、@Inject 注解。在 postProcessBeforeInitialization() 中扫描字段和Setter，注入依赖。
> - **CommonAnnotationBeanPostProcessor**：处理 @Resource、@PostConstruct、@PreDestroy 注解。
> - **RequiredAnnotationBeanPostProcessor**：处理 @Required 注解（已过时）。
> - **AnnotationAwareAspectJAutoProxyCreator**：在 postProcessAfterInitialization() 中生成 AOP 代理对象。
> - **ApplicationContextAwareProcessor**：处理 XxxAware 接口（BeanNameAware、BeanFactoryAware 等）。
> - **ConfigurationClassPostProcessor**：处理 @Configuration 注解，解析配置类。
>
> **自定义 BeanPostProcessor 的场景：**
>
> - 如果需要让某个 Bean 在初始化后打印日志：实现 BeanPostProcessor，在 postProcessAfterInitialization() 中检测目标 Bean 并执行操作。
> - 如果需要修改所有 Bean 的实例（比如包装成代理对象）：在 postProcessAfterInitialization() 中返回代理对象。

---

## 追问 2：Spring 中 AOP 代理的自动创建是如何触发的？

### 追问回答

> Spring AOP 代理的自动创建由 `AnnotationAwareAspectJAutoProxyCreator` 完成。
>
> **触发时机：**
>
> Bean 在初始化完成后（postProcessAfterInitialization() 阶段），AutoProxyCreator 检查当前 Bean 是否匹配任何 Advisor（切面）的 Pointcut。
>
> **Pointcut 匹配分两步：**
>
> - **类匹配**：检查目标类是否匹配切点表达式（看类上有没有 @Aspect 等注解）
> - **方法匹配**：检查类中的方法是否匹配切点表达式（如 `@annotation(Tx)` 匹配所有标注了 @Tx 的方法）
>
> **如果匹配成功：** 在 postProcessAfterInitialization() 中返回代理对象替换原始对象。
>
> **如果匹配失败：** 返回原始对象，不做代理。
>
> **面试加分点：** 可以提到 Pointcut 的组成——一个 Pointcut = ClassFilter（类匹配）+ MethodMatcher（方法匹配）。两者都匹配，才算真正匹配成功。

---

## 追问 3：Spring 三级缓存中，ObjectFactory.getObject() 具体调用了什么？

### 追问回答

> ObjectFactory.getObject() 实际调用的是 `SmartInstantiationAwareBeanPostProcessor.getEarlyBeanReference()`。
>
> **getEarlyBeanReference() 的作用：**
>
> - 如果 Bean 需要 AOP 代理，在这里生成代理对象
> - 如果 Bean 不需要代理，返回原始对象
> - 这是 AOP 和循环依赖冲突的解决点——提前生成代理，保证所有依赖方拿到的都是代理对象
>
> **源码逻辑：**
>
> ```java
> protected Object getEarlyBeanReference(String beanName, RootBeanDefinition mbd, Object bean) {
>     Object exposedObject = bean;
>     // 遍历所有 SmartInstantiationAwareBeanPostProcessor
>     for (SmartInstantiationAwareBeanPostProcessor bp : getBeanPostProcessors()) {
>         // 调用 getEarlyBeanReference，生成早期引用（可能是代理）
>         exposedObject = bp.getEarlyBeanReference(advised, exposedObject);
>     }
>     return exposedObject;
> }
> ```
>
> **面试加分点：** 可以提到 AnnotationAwareAspectJAutoProxyCreator 实现了 SmartInstantiationAwareBeanPostProcessor 接口，它的 getEarlyBeanReference() 方法负责提前生成 AOP 代理。

---

## 追问 4：Spring 为什么用构造器参数顺序解决循环依赖而不是参数名称？

### 追问回答

> 这个问题考察的是对 Spring 构造器注入和反射机制的深度理解。
>
> **Spring 如何确定构造器参数对应哪个 Bean？**
>
> Spring 首先按类型匹配——如果只有一个构造器参数是 UserService，就直接注入。如果有多个同类型参数（如 UserService 和 OrderService），Spring 会同时按参数名称（构造器参数名）匹配 Bean 名称。
>
> **参数名称从哪里来？**
>
> Java 编译时，默认情况下构造器参数名不会保留在字节码中（除非用 -parameters 编译）。Spring 通过 `LocalVariableTableParameterNameDiscoverer` 或 `StandardReflectionParameterNameDiscoverer` 获取参数名——如果编译时没有保留参数名（大多数情况），Spring 就会用 `@Autowired` 配合 `@Qualifier` 来精确指定。
>
> **Spring 推荐构造器注入的原因之一：** 构造器参数本身就是依赖契约——参数名称、类型、数量清晰可见。如果依赖关系复杂，构造器参数会很多，自然提醒开发者需要重构（拆分服务）。

---

## 追问 5：Spring 的事件机制是同步还是异步？如何在发布事件后异步处理？

### 追问回答

> Spring 的 ApplicationEventPublisher 默认是**同步**的——事件发布后，所有 Listener 在同一个线程中顺序执行。
>
> **如何实现异步事件处理？**
>
> **方式一：@Async 注解（Spring 3.x+）：**
>
> ```java
> @Component
> public class OrderEventListener {
>     @Async
>     @EventListener
>     public void handleOrderCreated(OrderCreatedEvent event) {
>         // 异步执行，不阻塞主线程
>         sendEmail(event.getOrder());
>     }
> }
> ```
>
> **方式二：@EventListener + TaskExecutor：**
>
> ```java
> @EventListener
> public void handleOrderCreated(OrderCreatedEvent event,
>         @Autowired TaskExecutor executor) {
>     executor.execute(() -> {
>         sendEmail(event.getOrder());
>     });
> }
> ```
>
> **面试加分点：** 可以提到 Spring 5.x 引入的 `ApplicationEventPublisher` 可以通过返回 `CompletableFuture` 实现异步事件的端到端支持。

---

## 追问 6：Spring 如何实现自定义 Scope？比如让 Bean 存活在一个请求的生命周期内？

### 追问回答

> Spring 允许自定义 Scope，只需要实现 `Scope` 接口并注册到容器中。
>
> **实现请求级别的 Scope：**
>
> ```java
> // 1. 定义请求 Scope
> public class RequestScope implements Scope {
>
>     private final ThreadLocal<Map<String, Object>> requestScope =
>         new ThreadLocal<Map<String, Object>>() {
>             @Override
>             protected Map<String, Object> initialValue() {
>                 return new HashMap<>();
>             }
>         };
>
>     @Override
>     public Object get(String name, ObjectFactory<?> objectFactory) {
>         return requestScope.get().computeIfAbsent(name, k -> objectFactory.getObject());
>     }
>
>     @Override
>     public Object remove(String name) {
>         return requestScope.get().remove(name);
>     }
>
>     @Override
>     public void registerDestructionCallback(String name, Runnable callback) {
>         // 请求结束时执行的销毁回调
>     }
>
>     @Override
>     public Object resolveContextualObject(String key) { return null; }
>     @Override
>     public String getConversationId() { return null; }
> }
>
> // 2. 注册到容器
> @Configuration
> public class ScopeConfig {
>     @Bean
>     public static CustomScopeConfigurer customScopeConfigurer() {
>         CustomScopeConfigurer configurer = new CustomScopeConfigurer();
>         configurer.addScope("request", new RequestScope());
>         return configurer;
>     }
> }
>
> // 3. 使用自定义 Scope
> @Component
> @Scope("request")
> public class RequestContextBean {
>     private final String id = UUID.randomUUID().toString();
>     public String getId() { return id; }
> }
> ```
>
> 同一个 HTTP 请求中注入的 RequestContextBean 实例 ID 相同，不同请求实例 ID 不同。
>
> **Spring 内置 Scope 原理：** Spring 内置的 request scope 和 session scope，就是通过 `RequestContextListener` / `RequestContextFilter` 绑定 ThreadLocal 来实现的。

---

## 追问 7：Spring Boot 如何实现多数据源配置？

### 追问回答

> 多数据源是生产环境的常见需求，Spring Boot 支持手动配置。
>
> **方案一：手动注册多个 DataSource（Spring Boot 1.x）：**
>
> ```java
> @Configuration
> public class DataSourceConfig {
>
>     @Primary  // 默认数据源必须有 @Primary
>     @Bean(name = "primaryDataSource")
>     public DataSource primaryDataSource() {
>         return DataSourceBuilder.create()
>             .url("jdbc:mysql://localhost:3306/primary_db")
>             .username("root")
>             .password("root")
>             .driverClassName("com.mysql.cj.jdbc.Driver")
>             .build();
>     }
>
>     @Bean(name = "secondaryDataSource")
>     public DataSource secondaryDataSource() {
>         return DataSourceBuilder.create()
>             .url("jdbc:mysql://localhost:3306/secondary_db")
>             .username("root")
>             .password("root")
>             .driverClassName("com.mysql.cj.jdbc.Driver")
>             .build();
>     }
> }
> ```
>
> **方案二：使用 @Qualifier 切换数据源：**
>
> ```java
> @Repository
> public class UserDao {
>     @Autowired
>     @Qualifier("primaryDataSource")  // 注入主数据源
>     private DataSource dataSource;
> }
>
> @Repository
> public class LogDao {
>     @Autowired
>     @Qualifier("secondaryDataSource")  // 注入从数据源
>     private DataSource dataSource;
> }
> ```
>
> **方案三：读写分离（ShardingSphere）：**
>
> ```yaml
> spring:
>   shardingsphere:
>     datasource:
>       ds-master:
>         url: jdbc:mysql://localhost:3306/rw_db
>       ds-slave-0:
>         url: jdbc:mysql://localhost:3306/rw_db_slave
>     rules:
>       read-write-split:
>         data-sources:
>           pr_ds:
>             write-data-source-name: ds-master
>             read-data-source-names: ds-slave-0
> ```
>
> **面试加分点：** 可以提到 @Primary 注解的作用——当一个接口有多个实现类（多个 DataSource）时，Spring 默认按类型注入。如果有两个相同类型的 Bean，必须有一个标注 @Primary，否则启动报错。

---

## 追问 8：Spring Boot 如何自定义自动配置？

### 追问回答

> 自定义 Starter 是展示架构设计能力的好题目。
>
> **步骤一：创建自动配置模块（xxx-spring-boot-autoconfigure）：**
>
> ```java
> @Configuration
> @ConditionalOnClass(MyService.class)           // 存在 MyService 类时才生效
> @ConditionalOnMissingBean(MyService.class)     // 不存在 MyService Bean 时才生效
> @EnableConfigurationProperties(MyServiceProperties.class)
> public class MyServiceAutoConfiguration {
>
>     @Bean
>     public MyService myService(MyServiceProperties properties) {
>         return new MyService(properties.getConfig());
>     }
> }
> ```
>
> **步骤二：在 META-INF/spring/xxx.imports 中注册（Spring Boot 2.7+）：**
>
> ```
> com.example.autoconfigure.MyServiceAutoConfiguration
> ```
>
> **步骤三：创建 Starter POM（xxx-spring-boot-starter）：**
>
> ```xml
> <dependencies>
>     <dependency>
>         <groupId>com.example</groupId>
>         <artifactId>xxx-spring-boot-autoconfigure</artifactId>
>         <version>1.0.0</version>
>     </dependency>
>     <dependency>
>         <groupId>com.example</groupId>
>         <artifactId>my-service</artifactId>
>         <version>1.0.0</version>
>     </dependency>
> </dependencies>
> ```
>
> **步骤四：使用方只需一行依赖：**
>
> ```xml
> <dependency>
>     <groupId>com.example</groupId>
>     <artifactId>xxx-spring-boot-starter</artifactId>
> </dependency>
> ```
>
> **面试加分点：** 可以提到 Starter 的核心价值——**开箱即用 + 零配置**。用户只需要引入依赖，框架自动完成所有配置。如果用户需要自定义配置，只需在 application.yml 中覆盖默认配置即可（通过 @ConfigurationProperties 绑定）。

---

## 追问 9：Spring MVC 的 Controller 是单例还是多例？线程安全吗？

### 追问回答

> **Controller 默认是单例的。**
>
> Spring MVC 中所有 Bean 默认都是单例，包括 Controller。这意味着同一个 Controller 实例被所有请求共享。
>
> **单例 Controller 的线程安全问题：**
>
> 如果 Controller 中没有可变成员变量（无状态设计），就是线程安全的。
>
> **危险写法：**
>
> ```java
> @RestController
> public class BadController {
>     private int count = 0;  // 危险！多线程共享，不安全
>
>     @GetMapping("/count")
>     public int count() {
>         return ++count;  // 多线程并发不安全
>     }
> }
> ```
>
> **安全写法（无状态设计）：**
>
> ```java
> @RestController
> public class GoodController {
>     @GetMapping("/count")
>     public int count(HttpServletRequest request) {
>         Integer count = (Integer) request.getSession().getAttribute("count");
>         count = (count == null ? 0 : count) + 1;
>         request.getSession().setAttribute("count", count);
>         return count;  // 线程安全：状态存在请求上下文/ThreadLocal中
>     }
> }
> ```
>
> **Spring 的设计哲学：** Controller 应当是无状态的，所有需要持久化的数据应该存到数据库、Session 或 ThreadLocal 中，而不是成员变量。
>
> **面试加分点：** 可以提到 Service 层同样适用——Service 默认也是单例的，线程安全规则和 Controller 完全一样。

---

## 追问 10：Spring 如何解决 Bean 循环依赖的检测？

### 追问回答

> Spring 在 Bean 创建前后的不同阶段使用不同机制检测循环依赖。
>
> **构造器循环依赖检测（启动时报错）：**
>
> Spring 在 `AbstractBeanFactory.doGetBean()` 中维护了一个 `Set<String> singletonsCurrentlyInCreation`，记录正在创建的 Bean 名称。
>
> 创建 Bean 之前，检查 Bean 名称是否已在集合中：
> - 如果在 → 抛出 `BeanCurrentlyInCreationException`（构造器循环依赖）
> - 如果不在 → 加入集合，开始创建
>
> **Setter 循环依赖检测（不报错但解决）：**
>
> Setter 循环依赖不报错，因为 Spring 有三级缓存机制。但是，Spring 在 `DefaultSingletonBeanRegistry` 中维护了 `Set<String> alreadyCreated`——记录所有尝试创建的 Bean。
>
> 如果发现同一个 Bean 在创建过程中（singletonsCurrentlyInCreation 中存在）又被请求创建，说明发生了循环依赖，此时 Spring 走三级缓存流程解决。
>
> **prototype 作用域循环依赖：**
>
> Spring 直接抛出异常，因为 prototype Bean 不走缓存，每次都重新创建。
>
> **面试加分点：** 可以提到，如果要检测代码中潜在的循环依赖，可以使用 Spring Boot Actuator 的 `/beans` 端点，结合 `BeanFactory.getDependenciesForBean()` 方法分析依赖关系图。

---

## 追问速查表

| 原题 | 追问 | 核心答案 |
|------|------|---------|
| Q6 Bean生命周期 | BeanPostProcessor有哪些实际应用？ | AutowiredAnnotationBeanPostProcessor(@Autowired)、AnnotationAwareAspectJAutoProxyCreator(AOP)、CommonAnnotationBeanPostProcessor |
| Q10 AOP源码 | Spring如何自动创建AOP代理？ | AnnotationAwareAspectJAutoProxyCreator在postProcessAfterInitialization()中检查Pointcut匹配，匹配则返回代理对象 |
| Q13 三级缓存 | ObjectFactory.getObject()具体调用了什么？ | SmartInstantiationAwareBeanPostProcessor.getEarlyBeanReference()，负责提前生成AOP代理 |
| Q14 三级缓存 | 为什么要二级缓存缓存首次结果？ | 避免每次获取都生成新的代理对象，保证所有依赖方拿到同一个代理 |
| Q15 构造器循环依赖 | Spring如何检测循环依赖？ | singletonsCurrentlyInCreation Set，Bean名称已在集合中说明发生循环依赖 |
| Q16 Spring Boot启动 | 自定义自动配置如何实现？ | @Configuration + @ConditionalOnClass + META-INF/spring/xxx.imports 注册 |
| Q20 事务失效 | 同类自调用的最佳解决方案？ | 拆分到不同类（推荐）、自我注入、使用AopContext.currentProxy() |
| Q23 Spring MVC | Controller是单例还是多例？线程安全吗？ | 默认单例，无状态则线程安全，有可变成员变量则不安全 |
| Q29 观察者模式 | Spring事件是同步还是异步？ | 默认同步，@Async注解使监听器异步执行 |
| 通用扩展 | 如何自定义Scope？ | 实现Scope接口 + CustomScopeConfigurer注册到容器 |

---

## 面试冲刺 · 核心要点速查表

| 模块 | 核心问题 | 关键答案 |
|------|---------|---------|
| IOC/DI | 三种注入方式 | 构造器（推荐）/ Setter / 字段，构造器可保证不可变性和快速暴露循环依赖 |
| Bean生命周期 | 初始化核心扩展点 | BeanPostProcessor前处理（注解注入）、BeanPostProcessor后处理（AOP代理） |
| AOP | JDK vs CGLIB | JDK（需接口/反射调用）、CGLIB（无需接口/直接调用更快）、Spring Boot 2.x默认CGLIB |
| AOP | 五种通知顺序 | Around→Before→目标方法→After→AfterReturning/AfterThrowing |
| 循环依赖 | 三级缓存原理 | 一级存成品、二级缓存早期引用（防重复生成代理）、三级存储ObjectFactory延迟创建 |
| 循环依赖 | 为什么需要三级？ | 二级无法解决AOP代理对象唯一性问题——每次调用ObjectFactory生成不同代理 |
| 循环依赖 | 构造器无法解决 | 实例化时就需要所有参数，没有机会提前暴露引用，死锁 |
| Spring Boot | 自动配置原理 | @Import(AutoConfigurationImportSelector) + @ConditionalOnClass + @ConfigurationProperties |
| Spring事务 | 失效8种 | 同类自调用、非public方法、异常被吞、checked异常不回滚、非容器管理对象、多线程、timeout过小、非InnoDB |
| 事务传播 | REQUIRED vs REQUIRES_NEW vs NESTED | REQUIRED共事务、REQUIRES_NEW独立事务、NESTED保存点子事务 |
| Spring MVC | 九大组件 | HandlerMapping查找Handler、HandlerAdapter执行、ViewResolver解析视图 |
| 设计模式 | 5种在Spring中的应用 | 策略（Resource接口）、工厂（FactoryBean）、模板方法（JdbcTemplate）、观察者（ApplicationEvent）、代理（AOP） |
| Filter vs Interceptor vs AOP | 粒度和顺序 | Filter（Servlet规范，最外层）→Interceptor（Spring MVC，中间）→AOP（Bean方法，最细） |
