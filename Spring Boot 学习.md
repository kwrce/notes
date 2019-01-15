# Spring Boot 学习

## 一、第一个HelloWorld

### 1.创建一个maven工程

pom文件,依赖：

```xml
<!-- Inherit defaults from Spring Boot -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.9.RELEASE</version>
    </parent>

    <!-- Add typical dependencies for a web application -->
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
```

### 2. 编写主程序启动SpringBoot 服务

```java
/**
 * 标注主程序
 */
@SpringBootApplication
public class HelloWorldApplication {
    public static void main(String[] args) {
        //使springboot启动起来
        SpringApplication.run(HelloWorldApplication.class).run(args);
    }
}

```

### 3.编写controller

```java
@Controller
public class HelloController {
    @ResponseBody
    @RequestMapping("/hello")
    public String hello(){
        return "Hello World!";
    }
}

```

### 4.简化部署

配置pom文件，打jar包

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

以lifecycle中的`package`方式启动

jar包使用`java -jar`的方式启动



## 二、结构理解

### 1.父项目

```xml
<parent>
<groupId>org.springframework.boot</groupId>
<artifactId>spring‐boot‐starter‐parent</artifactId>
<version>1.5.9.RELEASE</version>
</parent>
<!-- 他的父项目是 -->
<parent>
<groupId>org.springframework.boot</groupId>
<artifactId>spring‐boot‐dependencies</artifactId>
<version>1.5.9.RELEASE</version>
<relativePath>../../spring‐boot‐dependencies</relativePath>
</parent>
他来真正管理Spring Boot应用里面的所有依赖版本；
```

Spring Boot的版本仲裁中心

以后我们导入依赖默认是不需要写版本；（没有在dependencies里面管理的依赖自然需要声明版本号）

### 2.启动器

```xml
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring‐boot‐starter‐web</artifactId>
</dependency>
```

spring-boot-starter-web：

spring-boot-starter：spring-boot场景启动器；帮我们导入了web模块正常运行所依赖的组件；

Spring Boot将所有的功能场景都抽取出来，做成一个个的starters（启动器），只需要在项目里面引入这些starter

相关场景的所有依赖都会导入进来。要用什么功能就导入什么场景的启动器

### 3.主程序类，主入口类

```java
/**
* @SpringBootApplication 来标注一个主程序类，说明这是一个Spring Boot应用
*/
@SpringBootApplication
public class HelloWorldMainApplication {
public static void main(String[] args) {
// Spring应用启动起来
SpringApplication.run(HelloWorldMainApplication.class,args);
}
}
```

@SpringBootApplication: Spring Boot应用标注在某个类上说明这个类是SpringBoot的主配置类，SpringBoot就应该运行这个类的main方法来启动SpringBoot应用；

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = {
@Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {
```

@SpringBootConﬁguration:Spring Boot的配置类； 标注在某个类上，表示这是一个Spring Boot的配置类；
@Conﬁguration:配置类上来标注这个注解；
配置类 —– 配置文件；配置类也是容器中的一个组件；@Component
@EnableAutoConﬁguration：开启自动配置功能；
以前我们需要配置的东西，Spring Boot帮我们自动配置；@EnableAutoConﬁguration告诉SpringBoot开启自动配置功能；这样自动配置才能生效；

### 4.resource文件

- static：保存所有的静态资源； js css images；
- templates：保存所有的模板页面；（Spring Boot默认jar包使用嵌入式的Tomcat，默认不支持JSP页面）；可以使用模板引擎（freemarker、thymeleaf）；
- application.properties：Spring Boot应用的配置文件；可以修改一些默认设置；

## 三、配置文件

### 1、配置文件

SpringBoot使用一个全局的配置文件，配置文件名是固定的；

- application.properties
- application.yml

配置文件的作用：修改SpringBoot自动配置的默认值；(SpringBoot在底层都给我们自动配置好了)

YAML（YAML Ain’t Markup Language） YAML A Markup Language：是一个标记语言

YAML isn’t Markup Language：不是一个标记语言； 标记语言：

以前的配置文件；大多都使用的是 xxxx.xml文件；

YAML：以数据为中心，比json、xml等更适合做配置文件；

YAML：配置例子

```
server:
    port: 8081
```

XML例子：

```
<server>
    <port>8081</port>
</server>
```

### 2、YAML语法：

#### 1、基本语法

k:(空格)v：表示一对键值对（空格必须有）；

以空格的缩进来控制层级关系；只要是左对齐的一列数据，都是同一个层级的

```yaml
server:
    port: 8081
    path: /hello
```

属性和值是大小写敏感的；

#### 2、值的写法

字面量：普通的值（数字，字符串，布尔）

k: v：字面直接来写；

字符串默认不用加上单引号或者双引号；

“”：双引号；不会转义字符串里面的特殊字符；特殊字符会作为本身想表示的意思name: “zhangsan \n lisi”：输出；zhangsan 换行 lisi

”：单引号；会转义特殊字符，特殊字符最终只是一个普通的字符串数据

name: ‘zhangsan \n lisi’：输出；zhangsan \n lisi

对象、Map（属性和值）（键值对）：

k: v

：在下一行来写对象的属性和值的关系；注意缩进

对象还是k: v的方式

```yaml
friends:
    lastName: zhangsan
    age: 20 
```

行内写法：

```
friends: {lastName: zhangsan,age: 20}
```

数组（List、Set）：

用- 值表示数组中的一个元素

```yaml
pets:
  ‐ cat
  ‐ dog
  ‐ pig
```

行内写法

```yaml
pets: [cat,dog,pig]
```

#### 3、配置文件值注入

##### 3.1、配置文件:

```yaml
person:
    lastName: hello #此处 lastName 和 last-name 所表示的是一样的
    age: 18
    boss: false
    birth: 2017/12/12
    maps: {k1: v1,k2: 12}
    lists:
        ‐ lisi
        ‐ zhaoliu
    dog:
        name: 小狗
        age: 12
```

##### 3.2、javaBean：

```java
/**
* 将配置文件中配置的每一个属性的值，映射到这个组件中
* @ConfigurationProperties：告诉SpringBoot将本类中的所有属性和配置文件中相关的配置进行绑定；
* prefix = "person"：配置文件中哪个下面的所有属性进行一一映射
*
* 只有这个组件是容器中的组件，才能容器提供的@ConfigurationProperties功能；
*
*/
@Component
@ConfigurationProperties(prefix = "person")
public class Person {
private String lastName;
private Integer age;
private Boolean boss;
private Date birth;
private Map<String,Object> maps;
private List<Object> lists;
private Dog dog;
```

我们可以导入配置文件处理器，以后编写配置就有提示了

```xml
<!‐‐导入配置文件处理器，配置文件进行绑定就会有提示‐‐>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring‐boot‐configuration‐processor</artifactId>
    <optional>true</optional>
</dependency>
```

##### 3.3、@Value获取值和@ConﬁgurationProperties**获取值比较

```java
            @ConﬁgurationProperties @Value
```

功能 批量注入配置文件中的属性 一个个指定 
松散绑定（松散语法） 支持 不支持 
SpEL 不支持 支持 
JSR303数据校验 支持 不支持 
复杂类型封装 支持 不支持

配置文件yml还是properties他们都能获取到值；

如果说，我们只是在某个业务逻辑中需要获取一下配置文件中的某项值，使用@Value；

如果说，我们专门编写了一个javaBean来和配置文件进行映射，我们就直接使用@ConﬁgurationProperties；

##### 3.4、配置文件注入值数据校验

```java
@Component
@ConfigurationProperties(prefix = "person")
@Validated
public class Person {
/**
* <bean class="Person">
* <property name="lastName" value="字面量/${key}从环境变量、配置文件中获取值/#
{SpEL}"></property>
* <bean/>
*/
//lastName必须是邮箱格式
@Email
//@Value("${person.last‐name}")
private String lastName;
//@Value("#{11*2}")
private Integer age;
//@Value("true")
private Boolean boss;
private Date birth;
private Map<String,Object> maps;
private List<Object> lists;
private Dog dog;
```

##### 3.5、@PropertySource&@ImportResource&@Bean

@PropertySource：加载指定的配置文件；

```java
/**
* 将配置文件中配置的每一个属性的值，映射到这个组件中
* @ConfigurationProperties：告诉SpringBoot将本类中的所有属性和配置文件中相关的配置进行绑定；
* prefix = "person"：配置文件中哪个下面的所有属性进行一一映射
*
* 只有这个组件是容器中的组件，才能容器提供的@ConfigurationProperties功能；
* @ConfigurationProperties(prefix = "person")默认从全局配置文件中获取值；
*
*/
@PropertySource(value = {"classpath:person.properties"})
@Component
@ConfigurationProperties(prefix = "person")
//@Validated
public class Person {
/**
* <bean class="Person">
* <property name="lastName" value="字面量/${key}从环境变量、配置文件中获取值/#
{SpEL}"></property>
* <bean/>
*/
//lastName必须是邮箱格式
// @Email
//@Value("${person.last‐name}")
private String lastName;
//@Value("#{11*2}")
private Integer age;
//@Value("true")
private Boolean boss;
```

@ImportResource：导入Spring的配置文件，让配置文件里面的内容生效；

Spring Boot里面没有Spring的配置文件，我们自己编写的配置文件，也不能自动识别； 想让Spring

的配置文件生效，加载进来；需要将@ImportResource标注在一个配置类上

```java
@ImportResource(locations = {"classpath:beans.xml"})
导入Spring的配置文件让其生效
```

自定义的Spring配置文件

```xml
<?xml version="1.0" encoding="UTF‐8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
xmlns:xsi="http://www.w3.org/2001/XMLSchema‐instance"
xsi:schemaLocation="http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring‐beans.xsd">
<bean id="helloService" class="com.atguigu.springboot.service.HelloService"></bean>
</beans>
```

SpringBoot推荐给容器中添加组件的方式；推荐使用全注解的方式

1、配置类@Conﬁguration——>Spring配置文件

2、使用@Bean给容器中添加组件

```
/**
* @Configuration：指明当前类是一个配置类；就是来替代之前的Spring配置文件
* 在配置文件中用<bean><bean/>标签添加组件
*/
@Configuration
public class MyAppConfig {
//将方法的返回值添加到容器中；容器中这个组件默认的id就是方法名
@Bean
public HelloService helloService02(){
System.out.println("配置类@Bean给容器中添加组件了...");
return new HelloService();
}
}
```

##### 3.6、配置文件占位符

1、随机数

```yaml
${random.value}、${random.int}、${random.long}
${random.int(10)}、${random.int[1024,65536]}

```

2、占位符获取之前配置的值，如果没有可以是用:指定默认值

```yaml
person.last‐name=张三${random.uuid}
person.age=${random.int}
person.birth=2017/12/15
person.boss=false
person.maps.k1=v1
person.maps.k2=14
person.lists=a,b,c
person.dog.name=${person.hello:hello}_dog
person.dog.age=15

```

##### 3.7、Proﬁle

1、多Proﬁle文件

我们在主配置文件编写的时候，文件名可以是 application-{proﬁle}.properties/yml

默认使用application.properties的配置；

2、yml支持多文档块方式

```yaml
server:
    port: 8081
spring:
    profiles:
    active: prod
‐‐‐
server:
    port: 8083
spring:
    profiles: dev
‐‐‐
server:
    port: 8084
    spring:
profiles: prod #指定属于哪个环境

```

3、激活指定proﬁle

1、在配置文件中指定 spring.proﬁles.active=dev

2、命令行：

java -jar spring-boot-02-conﬁg-0.0.1-SNAPSHOT.jar –spring.proﬁles.active=dev； 可以直接在测试的时候，配置传入命令行参数

3、虚拟机参数；

-Dspring.proﬁles.active=dev

##### 3.8、配置文件加载位置

springboot 启动会扫描以下位置的application.properties或者application.yml文件作为Spring boot的默认配置文件

```yaml
–ﬁle:./conﬁg/

–ﬁle:./

–classpath:/conﬁg/

–classpath:/
1234567
```

优先级由高到底，高优先级的配置会覆盖低优先级的配置；

SpringBoot会从这四个位置全部加载主配置文件；互补配置；

我们还可以通过spring.conﬁg.location来改变默认的配置文件位置

项目打包好以后，我们可以使用命令行参数的形式，启动项目的时候来指定配置文件的新位置；指定配置文件和默  认加载的这些配置文件共同起作用形成互补配置；

java -jar spring-boot-02-conﬁg-02-0.0.1-SNAPSHOT.jar –spring.conﬁg.location=G:/application.properties

##### 3.9、外部配置加载顺序

SpringBoot也可以从以下位置加载配置；   优先级从高到低；高优先级的配置覆盖低优先级的配置，所有的配置会形成互补配置

1. 命令行参数 
    所有的配置都可以在命令行上进行指定 
    java -jar spring-boot-02-conﬁg-02-0.0.1-SNAPSHOT.jar –server.port=8087 –server.context-path=/abc 
    多个配置用空格分开； –配置项=值
2. 来 自 java:comp/env 的 JNDI 属 性  
3. Java系统属性（System.getProperties()）
4. 操作系统环境变量
5. RandomValuePropertySource配置的random.*属性值

由jar包外向jar包内进行寻找；优先加载带proﬁle

1. jar包外部的application-{proﬁle}.properties或application.yml(带spring.proﬁle)配置文件
2. jar包内部的application-{proﬁle}.properties或application.yml(带spring.proﬁle)配置文件

再来加载不带proﬁle

1. jar包外部的application.properties或application.yml(不带spring.proﬁle)配置文件

2. jar包内部的application.properties或application.yml(不带spring.proﬁle)配置文件

   10.@Conﬁguration注解类上的@PropertySource

   11.通过SpringApplication.setDefaultProperties指定的默认属性所有支持的配置加载来源；

参考官方文档

##### 3.10、自动配置原理

配置文件到底能写什么？怎么写？自动配置原理； 配置文件能配置的属性参照

1、自动配置原理：

1）、SpringBoot启动的时候加载主配置类，开启了自动配置功能 @EnableAutoConﬁguration

2）、@EnableAutoConﬁguration 作用：

- 利用EnableAutoConﬁgurationImportSelector给容器中导入一些组件？ 

- 可以查看selectImports()方法的内容；

- List conﬁgurations = getCandidateConﬁgurations(annotationMetadata, attributes);获取候选的配置

  SpringFactoriesLoader.loadFactoryNames() 
   扫描所有jar包类路径下 META‐INF/spring.factories 
   把扫描到的这些文件的内容包装成properties对象 
   从properties中获取到EnableAutoConfiguration.class类（类名）对应的值，然后把他们添加在容器 
   中

将 类路径下 META-INF/spring.factories 里面配置的所有EnableAutoConﬁguration的值加入到了容器中；

```yaml
# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\
org.springframework.boot.autoconfigure.amqp.RabbitAutoConfiguration,\
org.springframework.boot.autoconfigure.batch.BatchAutoConfiguration,\
org.springframework.boot.autoconfigure.cache.CacheAutoConfiguration,\
org.springframework.boot.autoconfigure.cassandra.CassandraAutoConfiguration,\
org.springframework.boot.autoconfigure.cloud.CloudAutoConfiguration,\
org.springframework.boot.autoconfigure.context.ConfigurationPropertiesAutoConfiguration,\
org.springframework.boot.autoconfigure.context.MessageSourceAutoConfiguration,\
org.springframework.boot.autoconfigure.context.PropertyPlaceholderAutoConfiguration,\
org.springframework.boot.autoconfigure.couchbase.CouchbaseAutoConfiguration,\
org.springframework.boot.autoconfigure.dao.PersistenceExceptionTranslationAutoConfiguration,
\
org.springframework.boot.autoconfigure.data.cassandra.CassandraDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.cassandra.CassandraRepositoriesAutoConfiguration
,\
org.springframework.boot.autoconfigure.data.couchbase.CouchbaseDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.couchbase.CouchbaseRepositoriesAutoConfiguration
,\
org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchAutoConfiguration,\
org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchDataAutoConfiguration
```

每一个这样的     xxxAutoConﬁguration类都是容器中的一个组件，都加入到容器中；用他们来做自动配置； 

3）、每一个自动配置类进行自动配置功能；                                                                         

4）、以HttpEncodingAutoConﬁguration（Http编码自动配置）为例解释自动配置原理；

```java
@Configuration //表示这是一个配置类，以前编写的配置文件一样，也可以给容器中添加组件
@EnableConfigurationProperties(HttpEncodingProperties.class) //启动指定类的
ConfigurationProperties功能；将配置文件中对应的值和HttpEncodingProperties绑定起来；并把
HttpEncodingProperties加入到ioc容器中
@ConditionalOnWebApplication //Spring底层@Conditional注解（Spring注解版），根据不同的条件，如果
满足指定的条件，整个配置类里面的配置就会生效； 判断当前应用是否是web应用，如果是，当前配置类生效
@ConditionalOnClass(CharacterEncodingFilter.class) //判断当前项目有没有这个类
CharacterEncodingFilter；SpringMVC中进行乱码解决的过滤器；
@ConditionalOnProperty(prefix = "spring.http.encoding", value = "enabled", matchIfMissing =
true) //判断配置文件中是否存在某个配置 spring.http.encoding.enabled；如果不存在，判断也是成立的
//即使我们配置文件中不配置pring.http.encoding.enabled=true，也是默认生效的；
public class HttpEncodingAutoConfiguration {
//他已经和SpringBoot的配置文件映射了
private final HttpEncodingProperties properties;
    //只有一个有参构造器的情况下，参数的值就会从容器中拿
public HttpEncodingAutoConfiguration(HttpEncodingProperties properties) {
this.properties = properties;
}
@Bean //给容器中添加一个组件，这个组件的某些值需要从properties中获取
@ConditionalOnMissingBean(CharacterEncodingFilter.class) //判断容器没有这个组件？
public CharacterEncodingFilter characterEncodingFilter() {
CharacterEncodingFilter filter = new OrderedCharacterEncodingFilter();
filter.setEncoding(this.properties.getCharset().name());
filter.setForceRequestEncoding(this.properties.shouldForce(Type.REQUEST));
filter.setForceResponseEncoding(this.properties.shouldForce(Type.RESPONSE));
return filter;
}
```

根据当前不同的条件判断，决定这个配置类是否生效？

一但这个配置类生效；这个配置类就会给容器中添加各种组件；这些组件的属性是从对应的properties类中获取 的，这些类里面的每一个属性又是和配置文件绑定的；

5）、所有在配置文件中能配置的属性都是在xxxxProperties类中封装者‘；配置文件能配置什么就可以参照某个功 能对应的这个属性类

```yaml
@ConfigurationProperties(prefix = "spring.http.encoding") //从配置文件中获取指定的值和bean的属
性进行绑定
public class HttpEncodingProperties {
public static final Charset DEFAULT_CHARSET = Charset.forName("UTF‐8");

```

精髓：

1）、SpringBoot启动会加载大量的自动配置类

2）、我们看我们需要的功能有没有SpringBoot默认写好的自动配置类；

3）、我们再来看这个自动配置类中到底配置了哪些组件；（只要我们要用的组件有，我们就不需要再来配置了）

4）、给容器中自动配置类添加组件的时候，会从properties类中获取某些属性。我们就可以在配置文件中指定这些属性的值；

xxxxAutoConﬁgurartion：自动配置类； 给容器中添加组件

xxxxProperties: 封装配置文件中相关属性；

2、细节

1、@Conditional派生注解（Spring注解版原生的@Conditional作用）

作用：必须是@Conditional指定的条件成立，才给容器中添加组件，配置里面的所有内容才生效；

   @Conditional扩展注解                  作用（判断是否满足当前指定条件） 
   @ConditionalOnJava                系统的java版本是否符合要求 
   @ConditionalOnBean                容器中存在指定Bean； 
   @ConditionalOnMissingBean         容器中不存在指定Bean； 
   @ConditionalOnExpression          满足SpEL表达式指定 
   @ConditionalOnClass               系统中有指定的类 
   @ConditionalOnMissingClass        系统中没有指定的类 
   @ConditionalOnSingleCandidate     容器中只有一个指定的Bean，或者这个Bean是首选Bean 
   @ConditionalOnProperty            系统中指定的属性是否有指定的值 
   @ConditionalOnResource            类路径下是否存在指定资源文件 
   @ConditionalOnWebApplication      当前是web环境 
   @ConditionalOnNotWebApplication   当前不是web环境 
   @ConditionalOnJndi                JNDI存在指定项                     

自动配置类必须在一定的条件下才能生效； 我们怎么知道哪些自动配置类生效；

我们可以通过启用   **debug=true** 属性；来让控制台打印自动配置报告，这样我们就可以很方便的知道哪些自动配置类生效；

```yaml
=========================
AUTO‐CONFIGURATION REPORT
=========================
Positive matches:（自动配置类启用的）
‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐
DispatcherServletAutoConfiguration matched:
‐ @ConditionalOnClass found required class
'org.springframework.web.servlet.DispatcherServlet'; @ConditionalOnMissingClass did not find
unwanted class (OnClassCondition)
‐ @ConditionalOnWebApplication (required) found StandardServletEnvironment
(OnWebApplicationCondition)
Negative matches:（没有启动，没有匹配成功的自动配置类）
‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐
ActiveMQAutoConfiguration:
Did not match:
‐ @ConditionalOnClass did not find required classes 'javax.jms.ConnectionFactory',
'org.apache.activemq.ActiveMQConnectionFactory' (OnClassCondition)
AopAutoConfiguration:
Did not match:
‐ @ConditionalOnClass did not find required classes
'org.aspectj.lang.annotation.Aspect', 'org.aspectj.lang.reflect.Advice' (OnClassCondition)
```

 