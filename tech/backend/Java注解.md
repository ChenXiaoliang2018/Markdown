## Java注解

## @SpringBootApplication

@SpringBootApplication是Spring Boot框架中的核心注解，用于标记应用程序的主类，简化了Spring Boot应用的配置和启动流程。它是一个组合注解，主要由以下三个注解构成：

### 核心构成注解
@SpringBootConfiguration：相当于@Configuration，表明当前类是一个配置类，用于定义Bean。

@EnableAutoConfiguration：启用Spring Boot的自动配置机制，根据类路径中的依赖和已有Bean的定义，自动配置应用程序。

@ComponentScan：启用组件扫描，默认扫描当前包及其子包中带有@Component、@Service、@Repository、@Controller等注解的类，并将其注册为Spring Bean。

### 使用示例
```java
@SpringBootApplication
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```

### 常用属性
exclude：排除指定的自动配置类。

scanBasePackages：指定组件扫描的基础包。

### 总结
@SpringBootApplication通过整合多个注解，实现了Spring Boot应用的快速配置和启动，体现了“约定优于配置”的理念，提高了开发效率。@SpringBootApplication 注解详解：Spring Boot 应用的核心入口-CSDN技术社区

在 Spring Boot 开发中，@SpringBootApplication注解是所有应用的起点，它如同一个"魔法开关"，开启了 Spring Boot 的自动配置、组件扫描和配置类功能。这个看似简单的注解背后，隐藏着 Spring Boot"约定优于配置"理念的核心...

## @Configuration
@Configuration是Spring框架中的一个注解，用于标记一个类作为配置类，替代传统的XML配置文件来定义和配置应用程序中的Bean。以下是@Configuration注解的主要特点和用法：

### 主要特点
定义Bean：在配置类中，可以使用@Bean注解的方法来定义Bean，这些方法将被Spring容器管理，用于生成Bean定义和服务请求。

依赖注入：通过@Bean方法，可以清晰地定义Bean之间的依赖关系，Spring容器会自动处理这些依赖。

组件扫描：@Configuration类本身也可以被@ComponentScan扫描到，因为@Configuration使用@Component进行元注解。

CGLIB代理：Spring会使用CGLIB动态代理对@Configuration类进行增强，确保@Bean方法返回的是容器中的单例Bean，而不是每次都新建一个实例。

### 基本用法
声明配置类：使用@Configuration注解标记一个类，表明这是一个配置类。

```java
@Configuration
public class AppConfig {
    // 在这里定义Bean
}
```

定义Bean：在配置类中使用@Bean注解的方法来定义Bean。

```java
@Configuration
public class AppConfig {
    @Bean
    public MyService myService() {
        return new MyService();
    }
    // 可以依赖其他Bean
    @Bean
    public AnotherService anotherService(MyService myService) {
        return new AnotherService(myService);
    }
}
```

启动配置类：可以通过AnnotationConfigApplicationContext来引导启动这个被@Configuration注解的类。

```java
public static void main(String[] args) {
    ApplicationContext ctx = new AnnotationConfigApplicationContext(AppConfig.class);
    // 使用ctx获取Bean
}
```

### 示例
假设有一个简单的应用程序，需要定义一个数据源和一个使用数据源的服务。

定义数据源Bean

```java
@Configuration
public class DataSourceConfig {
    @Bean
    public DataSource dataSource() {
        // 创建并配置数据源
        DriverManagerDataSource dataSource = new DriverManagerDataSource();
        dataSource.setDriverClassName("com.mysql.jdbc.Driver");
        dataSource.setUrl("jdbc:mysql://localhost:3306/mydb");
        dataSource.setUsername("myuser");
        dataSource.setPassword("mypassword");
        return dataSource;
    }
}
```

定义服务Bean，并依赖数据源

```java
@Configuration
public class ServiceConfig {
    @Bean
    public MyService myService(DataSource dataSource) {
        // 创建并配置服务，注入数据源
        MyService service = new MyServiceImpl();
        service.setDataSource(dataSource);
        return service;
    }
}
```

启动应用程序

```java
public class Application {
    public static void main(String[] args) {
        ApplicationContext ctx = new AnnotationConfigApplicationContext(DataSourceConfig.class, 	ServiceConfig.class);	      
        MyService myService = ctx.getBean(MyService.class);
        // 使用myService
    }
}
```

通过@Configuration注解，可以更加灵活和强大地配置Spring应用程序，利用Java代码的丰富表达力来管理Bean的定义和依赖关系。

## @MapperScan
是MyBatis-Spring项目中的一个注解，主要用于自动扫描指定包下的Mapper接口，并将这些接口注册为Spring容器中的Bean。以下是关于@MapperScan的详细介绍：

### 作用
简化MyBatis与Spring Boot集成时的配置工作，特别是在中大型项目中，减轻开发人员负担，确保持久化层的整洁性和可维护性。

### 使用方法
放置在Spring Boot启动类或配置类上，通过basePackages或value属性指定需要扫描的包路径。

可扫描多个包，使用数组形式指定多个包名。

支持通配符，如com.example.**.mapper表示扫描com.example包及其所有子包下的mapper包。

### 底层实现原理
当Spring上下文启动时，@MapperScan注解会被处理，通常涉及到ClassPathMapperScanner类，它继承自Spring的ClassPathScanningCandidateComponentProvider，并重写了一些方法以适应MyBatis Mapper的特定需求。

对于每一个找到的Mapper接口，ClassPathMapperScanner会为其创建一个MapperFactoryBean实例，并将其注册到Spring容器中。

利用动态代理技术，当应用程序请求某个Mapper接口类型的Bean时，Spring会返回由MapperFactoryBean创建的代理对象，该代理对象能够拦截方法调用，并将其转发给MyBatis的SqlSession，从而执行相应的数据库操作。

### 注意事项
确保@MapperScan注解指定的包路径正确，包含所有需要注册的Mapper接口，否则会导致Mapper接口未被识别，出现No qualifying bean of type 'xxxMapper' available等报错。

如果项目中存在多个@MapperScan注解，需注意避免包路径冲突或重复扫描。

若Mapper接口未标注@Mapper注解，需确保@MapperScan注解正确配置且包含该接口所在的包路径。

总之，@MapperScan注解通过自动扫描指定包下的Mapper接口并将其注册为Spring容器中的Bean，极大地简化了MyBatis与Spring Boot的集成配置，提高了开发效率和代码的可维护性。

## @Data 
是 Lombok 库中的一个注解，主要用于简化 Java 类的代码编写过程。通过在类上添加 @Data 注解，Lombok 会自动为该类生成以下方法：

Getter 和 Setter 方法：为类的所有字段自动生成对应的 getter 和 setter 方法。

toString() 方法：生成一个将对象转换为字符串的方法，方便日志输出和调试。

equals() 和 hashCode() 方法：用于比较两个对象是否相等以及计算对象的哈希值。

无参构造函数：生成一个不带参数的构造方法。

@Data可以显著减少代码量，使代码更加简洁易读，避免编写重复的代码，降低出错的风险，提高开发效率。通常用在包含属性和简单逻辑的 POJO（Plain Old Java Object）类上。

## @AllArgsConstructor
@AllArgsConstructor是Lombok提供的一个注解，用于自动生成一个包含所有成员变量作为参数的构造方法。以下是关于@AllArgsConstructor的详细介绍：

### 作用
生成一个全参构造器，参数的顺序与类中字段定义的顺序一致。

#### 使用示例
```java
import lombok.AllArgsConstructor;
@AllArgsConstructor
public class Person {
    private String name;
    private int age;
    private String address;
}
```

在上述示例中，@AllArgsConstructor会生成一个包含所有成员变量的构造方法：

```java
public Person(String name, int age, String address) {
    this.name = name;
    this.age = age;
    this.address = address;
}
```

#### 注意事项
与@Builder配合使用：@Builder注解会自动生成一个private的全参隐式构造器供自己的build()方法使用，实现链式编程构建对象。如果类中有手写的构造方法，会导致@Builder原有的隐式全参构造器失效。此时，可以搭配@AllArgsConstructor使用，为@Builder提供支持，让build()方法有显式的全参构造器可以使用，实现链式编写。

与@NoArgsConstructor同时使用：当类中同时存在@AllArgsConstructor和@NoArgsConstructor时，可能会引发编译错误，因为这两个注解生成的构造方法在功能上存在冲突。如果确实不需要无参构造函数，应删除@NoArgsConstructor注解，只保留@AllArgsConstructor，以确保只有带全部参数的构造函数被创建，减少潜在冲突的可能性。

优先级：如果存在任何显式构造器，Lombok不会生成默认构造器。@AllArgsConstructor的优先级高于@Data包含的@RequiredArgsConstructor，总是显式生成全参构造器。

#### 常见问题及解决方案
注解失效：如果@AllArgsConstructor注解不起作用，可能是由于Lombok插件未正确安装或配置、IDE缓存问题、构建工具配置错误等原因导致的。可以检查Lombok插件是否启用、清理IDE缓存并重启、确保构建工具正确识别Lombok依赖项等。

版本冲突：Lombok版本与IDE版本或其他依赖的版本不兼容，也可能导致@AllArgsConstructor注解失效。可以尝试更新Lombok和IDE到最新的稳定版本，或者调整相关依赖的版本，以解决版本冲突问题。

总之，@AllArgsConstructor注解可以简化代码，自动生成全参构造方法，但在使用时需要注意与其他注解的搭配和潜在的冲突，以确保代码的正确性和可读性。

## @RestController
@RestController是Spring框架中用于简化RESTful Web服务开发的注解，它结合了@Controller和@ResponseBody两个注解的功能。以下是@RestController的详细介绍：



### 核心功能
控制器标识：表明该类是一个Spring MVC控制器，能够处理HTTP请求。

自动响应体转换：所有方法返回值都会自动通过HTTP消息转换器转换为JSON/XML等格式，直接写入HTTP响应体，而不需要经过视图解析。

### 使用场景
开发前后端分离的RESTful API，返回JSON或XML格式的数据。

构建微服务架构中的服务端点。

### 使用方法
在类级别添加@RestController注解，定义该类为REST控制器。

使用@GetMapping、@PostMapping等注解映射请求路径和方法。

方法返回的对象会被自动转换为JSON或其他配置的格式。

### 注意事项
确保项目中包含JSON序列化库（如Jackson）等依赖。

使用@RestController后，Spring不会进行视图解析，因此不能返回视图名称，只能返回数据对象。

可以通过ResponseEntity自定义响应状态码和头信息。

建议配合@ControllerAdvice全局处理异常，返回统一的错误格式。

总之，@RestController是Spring生态中构建REST API的核心工具，通过减少样板代码显著提升开发效率。在微服务架构和前后端分离项目中，该注解的应用尤为广泛。

## @RequestMapping
@RequestMapping是Spring Web MVC中用于处理请求地址映射的核心注解，它可以应用于类或方法上。以下是关于@RequestMapping的详细介绍：



### 作用
建立请求URL和处理请求方法之间的映射关系。

### 位置
类上：定义请求URL的第一级访问目录，作为类中所有响应请求的方法的父路径。

方法上：定义请求URL的第二级访问目录，与类上的@RequestMapping共同组成完整的访问路径。

### 属性
value：指定请求的URL，支持具体的值、URI模板模式（包含变量和正则表达式）。

method：指定请求的HTTP方法，如GET、POST、PUT、DELETE等。

params：指定请求参数的条件，要求请求参数的key和value与配置一致。

headers：指定请求头的要求。

consumes：指定处理请求的提交内容类型（Content-Type），如application/json。

produces：指定返回的内容类型，仅当请求头中的Accept类型包含指定类型时返回。

### 示例
类级别注解：@RequestMapping("/api/v1")，为整个控制器设置统一版本号前缀。

方法级别注解：@RequestMapping(value = "/order/{id}", method = RequestMethod.GET)，仅允许GET请求访问/order/{id}路径。

### 简化注解
Spring提供了更简洁的注解来替代@RequestMapping(method=XXX)，如@GetMapping、@PostMapping、@PutMapping、@DeleteMapping等。

### 注意事项
当请求地址满足value属性但请求方式不满足method属性时，会返回405错误。

使用@RequestMapping时，URL路径前加不加/均可，Spring会自动处理。

通过@RequestMapping注解，开发者可以灵活地定义Web请求与控制器方法之间的映射关系，实现RESTful风格的Web服务。

## @PostMapping
@PostMapping是Spring MVC提供的一个注解，用于处理HTTP POST请求。它通常用在控制器方法上，以声明该方法负责处理特定的POST请求。以下是关于@PostMapping的详细介绍：



### 基本用法
@PostMapping注解可以简写@RequestMapping(value="/path", method = RequestMethod.POST)，它指定了请求的HTTP方法为POST，并可以指定请求的URL路径。

当客户端发送POST请求到指定的URL路径时，Spring MVC会将请求映射到标注了@PostMapping的方法上进行处理。

#### 参数接收
使用@RequestBody注解可以将HTTP请求体的内容绑定到一个Java对象上，通常用于接收JSON或XML格式的数据。

使用@RequestParam注解可以从请求的URL查询参数或表单数据中提取值，通常用于处理简单的键值对数据。

#### 返回值处理
方法的返回值可以是任意类型，Spring MVC会根据情况自动将其转换为HTTP响应的内容。

如果返回值是ResponseEntity类型，可以更精细地控制HTTP响应的状态码、头部和内容。

### 示例
以下是一个使用@PostMapping注解的示例代码：

```java
@RestController
@RequestMapping("/users")
public class UserController {
    // 处理POST /users请求，创建新用户
    @PostMapping
    public User createUser(@RequestBody User user) {
        // 执行创建用户的逻辑
        return user;
    }    
    // 处理POST /users/{id}/avatar请求，上传用户头像
    @PostMapping("/{id}/avatar")
    public void uploadAvatar(@PathVariable Long id, @RequestParam("file") MultipartFile file) {
        // 执行上传头像的逻辑
    }
}
```

### 注意事项
@PostMapping注解的方法通常用于创建新资源，这是RESTful API设计中的一个常见实践。

在处理POST请求时，需要注意请求体的内容类型（Content-Type），以确保正确地解析请求数据。

通过@PostMapping注解，开发者可以更加简洁地编写处理POST请求的代码，提高代码的可读性和可维护性。

## @Service
@Service是Spring框架中的一个注解，主要用于标记服务层组件，通常用于业务逻辑层。以下是关于@Service注解的详细介绍：

### 作用
标识服务层：用于标注服务层组件，有助于区分不同的层（如数据访问层、Web层等）。

自动检测和装配：结合组件扫描功能，使得Spring可以自动检测到标有该注解的类，并将其注册为Spring应用上下文中的Bean。

AOP支持：使用@Service可以让开发者更容易地对这些方法进行事务管理、日志记录等横切关注点的处理。

### 使用
在类定义上添加@Service注解即可，如果需要指定Bean的名字，可以通过value属性来设置。

使用场景

业务逻辑处理：当有类专门用于处理业务逻辑时，例如计算价格、处理订单等，适合用@Service注解。

服务层抽象：为实现分层架构设计，将服务层与数据访问层（DAO层）分离，使用@Service标注服务层接口或其实现类。

### 内在运行逻辑
组件扫描：Spring Boot应用启动时，会自动扫描指定基础包下的所有类，若发现被@Service注解标记的类，则创建其实例并注册到Spring应用上下文中作为Bean。

依赖注入：被注册为Spring管理的Bean后，可以通过构造器注入、字段注入或setter方法注入等方式，在其他组件中使用，例如控制器（Controller）或其他服务（Service）。

AOP增强：Spring AOP可根据配置对带有@Service注解的Bean的方法进行拦截，从而在不修改原有业务逻辑的情况下增加额外功能，如事务管理、性能监控等。



综上所述，@Service注解在Spring框架中扮演着关键角色，它主要用于标识服务层组件，使得这些组件能够被Spring容器自动检测、管理和注入依赖。通过使用@Service注解，开发者可以更加专注于业务逻辑的实现，而无需过多关注底层的技术细节。

## @Autowired
@Autowired是Spring框架中的一个注解，用于实现依赖注入（Dependency Injection, DI）。以下是关于@Autowired的详细介绍：

### 功能与作用
自动装配：根据类型（默认）或名称（配合@Qualifier）自动注入Bean，减少手动配置。

简化代码：消除传统的setter和构造器手动赋值代码，提高代码可维护性。

支持多种注入方式：包括字段注入、构造器注入、Setter方法注入及方法参数注入。

### 使用方式
字段注入：直接在类的字段上标注@Autowired，Spring容器自动注入匹配类型的Bean。

Setter方法注入：在Setter方法上标注@Autowired，允许动态变更依赖。

构造器注入：在构造器上标注@Autowired，推荐方式，确保依赖不可变。

方法参数注入：在方法参数上标注@Autowired，适用于复杂初始化逻辑。

### 注入规则与高级用法
类型匹配与冲突解决：默认按类型匹配，若存在多个同类型Bean，需配合@Qualifier("beanName")指定名称。使用@Primary注解标记首选Bean，解决多个候选Bean的冲突。

required属性：默认值为true，表示如果没有合适的Bean可以注入，则会抛出异常。设置为false时，表示可以暂时不实例化。

### 注意事项
循环依赖：尽量避免循环依赖，如果无法避免，可以使用@Lazy注解延迟初始化。

与@Resource的区别：@Resource是JNDI规范的一部分，通常基于名称匹配来查找目标Bean。如果没有找到与名字完全一致的Bean，则会退回到按类型匹配的方式。

通过合理使用@Autowired注解，可以简化Spring应用程序的配置，提高代码的可读性和可维护性。

## @Override
@Override是Java中的一个注解（Annotation），用于指示一个方法重写了其父类中的方法。以下是@Override注解的一些主要用途和注意事项：

### 主要用途
明确意图：使用@Override可以清楚地表明开发者的意图，即该方法是为了覆盖父类中的方法，提高代码的可读性。

错误检查：如果方法签名与父类中的方法不匹配，或者父类中没有相应的方法可以被覆盖，编译器会报错，帮助开发者避免常见的错误。

### 注意事项
只能用于方法上，不能用于字段或其他成员。

必须确保重写的方法与父类中的方法具有相同的名称、参数列表和返回类型（除了协变返回类型的情况）。

如果父类中的方法是final的，那么不能被覆盖。

如果父类的方法是static的，子类中覆盖的方法不能使用@Override注解，因为静态方法不能被覆盖，而是被隐藏。

建议在所有打算重写父类方法的地方加上@Override注解，这是一种防御性编程手段，有助于提高代码的健壮性和可维护性。









