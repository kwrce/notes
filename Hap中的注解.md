# Hap中的注解

## 四大元注解：

### @Target：定义注解的作用目标

该注解用于什么地方，可能的值在枚举类 ElemenetType 中，包括： 

​          ElemenetType.CONSTRUCTOR---------构造器声明 

​          ElemenetType.FIELD -------------------域声明（包括 enum 实例） 

​          ElemenetType.LOCAL_VARIABLE------ 局部变量声明 

​          ElemenetType.METHOD ---------------方法声明 

​          ElemenetType.PACKAGE -------------- 包声明 

​          ElemenetType.PARAMETER -----------参数声明 

​          ElemenetType.TYPE------------------- 类，接口（包括注解类型）或enum声明

#### @Target注解代码：

```java
package java.lang.annotation;
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.ANNOTATION_TYPE)
public @interface Target {
    /**
     * Returns an array of the kinds of elements an annotation type
     * can be applied to.
     * @return an array of the kinds of elements an annotation type
     * can be applied to
     */
    ElementType[] value();
}
```

#### ElementType注解代码：

```java
package java.lang.annotation;
public enum ElementType {
    /** 类, 借口 (包括注解类型), 或者枚举声明 */
    TYPE,

    /** 字段声明(包括枚举常量) */
    FIELD,

    /** 方法声明 */
    METHOD,

    /** 参数声明 */
    PARAMETER,

    /** 构造声明 */
    CONSTRUCTOR,

    /** 局部变量声明 */
    LOCAL_VARIABLE,

    /** 注解声明 */
    ANNOTATION_TYPE,

    /** 包声明 */
    PACKAGE,

    /**
     * Type parameter declaration
     *
     * @since 1.8
     */
    TYPE_PARAMETER,

    /**
     * Use of a type
     *
     * @since 1.8
     */
    TYPE_USE
}
```

### @Retention：在什么级别保存该注解信息。

可选的参数值在枚举类型 RetentionPolicy 中，包括： 

​          RetentionPolicy.SOURCE -------------注解将被编译器丢弃 

​          RetentionPolicy.CLASS ---------------注解在class文件中可用，但会被VM丢弃 

​          RetentionPolicy.RUNTIME VM-------将在运行期也保留注释，因此可以通过反射机制读取注解的信息。

#### @Retention 代码：

```java
package java.lang.annotation;
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.ANNOTATION_TYPE)
public @interface Retention {
    /**
     * Returns the retention policy.
     * @return the retention policy
     */
    RetentionPolicy value();
}

```

#### RetentionPolicy代码

```java
package hbi.core.demo.controllers;
public enum RetentionPolicy {
    /**
     * 编译的时候被丢弃
     */
    SOURCE,

    /**
     *在class中可以用，但是在编译的时候会被丢弃
     */
    CLASS,

    /**
     * 在class中可以使用，在编译的时候会被保存
     * 故可以使用反射机制读取注解信息
     * @see java.lang.reflect.AnnotatedElement
     */
    RUNTIME
}
```



### @Documented：抽取文档

将此注解包含在 javadoc 中 ，它代表着此注解会被javadoc工具提取成文档。在doc文档中的内容会因为此注解的信息内容不同而不同。 相当与@see,@param 等。

#### @Documented代码：

```java
package java.lang.annotation;
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.ANNOTATION_TYPE)
public @interface Documented {
}

```

### @Inherited 允许子类继承父类中的注解。

```java
package java.lang.annotation;
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.ANNOTATION_TYPE)
public @interface Inherited {
}
```

## Spring MVC中的注解

### @Component

指示注释类是“组件”。 当使用基于注释的配置和类路径扫描时，这些类被认为是自动检测的候选对象。

### @Controller

@Controller表示注释的类是“控制器”（例如Web控制器）。这个注解作为@Component的一个特定方式存在，允许通过类路径扫描来自动检测实现类。通常情况下会结合RequestMapping注解使用。

### @Service

表示注释类是一个“服务”，最初由Domain-Driven Design （Evans，2003）定义为“作为模型中独立的接口提供的操作，没有封装状态”。

在一般情况下，我们把他用在标准我们的service服务接口的实现类上面，实际上这相当于缩小它们的语义和适当的使用。

@Service这个注释作为 @Component的一个特例，允许通过类路径扫描来自动检测实现类。

### @Repository

用于标注数据访问组件，即DAO组件

### @Autowired

@Autowired注解的意思就是，当Spring发现@Autowired注解时，将自动在代码上下文中找到和其匹配（默认是类型匹配）的Bean，并自动注入到相应的地方去。

但是当接口存在两个实现类的时候必须使用@Qualifier指定注入哪个实现类，否则可以省略，只写@Autowired。

### @Resource

@Resource是javax包中的注解，放在这里是为了和@Autowired做一个区别。@Resource默认按名称装配，当找不到与名称匹配的bean才会按类型装配。

### @Qualifier

@Qualifier用于指定注入Bean的名称，就是上面说到的，如果容器中有一个以上匹配的Bean，则可以通过@Qualifier注解限定Bean的名称。

### @PathVariable

当使用@RequestMapping URI template 样式映射时， 即 someUrl/{paramId}, 这时的paramId可通过 @Pathvariable注解绑定它传过来的值到方法的参数上。

例如：

```java
@RequestMapping("/user/{userId}")
public ModelAndView userCenter(HttpServletRequest request,
HttpServletResponse response,
@PathVariable String userId) {
//do something
}
```

### @RequestMapping

```java
package org.springframework.web.bind.annotation;
@Target({ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Mapping
public @interface RequestMapping {
    String name() default "";

    @AliasFor("path")
    String[] value() default {};

    @AliasFor("value")
    String[] path() default {};

    RequestMethod[] method() default {};

    String[] params() default {};

    String[] headers() default {};

    String[] consumes() default {};

    String[] produces() default {};
}
```

@RequestMapping是一个用来处理地址映射请求的注解，从定义可以看出，可作用于方法或者类上。

用于类上，大多数是为了进行区分controller

用于方法上则是对方法进行注解以产生访问的路径。它包括了几个属性：

value 用于设置方法或者类的映射路径，可以直接写路径。我们通常都是直接写，例如：@RequestMapping("/XXX");

method 用于指定请求的方法，可以设置单个或多个，如果请求方法不满足条件则会请求失败。

params 指定request中必须包含某些参数值是，才让该方法处理。

name 此映射指定一个名称

path   仅在Servlet环境中：路径映射URI（例如“/myPath.do”）。也支持Ant风格的路径模式（例如“/myPath/*.do”）。在方法级别，在类型级别表示的主映射内支持相对路径（例如“edit.do”）。  路径映射URI可能包含占位符（例如“/ $ {connect}”）

consumes 指定处理请求的提交内容类型（Content-Type），例如application/json, text/html;

produces 指定返回的内容类型，仅当request请求头中的(Accept)类型中包含该指定类型才返回；

headers 指定request中必须包含某些指定的header值，才能让该方法处理请求。

### @RequestBody

@RequestBody是作用在形参列表上，用于将前台发送过来固定格式的数据【xml 格式或者 json等】封装为对应的 JavaBean 对象，封装时使用到的一个对象是系统默认配置的 HttpMessageConverter进行解析，然后封装到形参上,支持联级。

例如：

```java
@RequestMapping("/login.do")
@ResponseBody
public Object login(@RequestBody User loginUuser, HttpSession session) {
    user = userService.checkLogin(loginUser);
    session.setAttribute("user", user);
    return new JsonResult(user);
}
```

### @ResponseBody

@ResponseBody这个一般是用在做异步请求调用的方法上来使用的。因为在使用@RequestMapping后，返回值通常解析为跳转路径。加上@responsebody后，返回结果直接写入HTTP response body中，不会被解析为跳转路径。

对于异步请求，我们不希望返回解析视图，二是希望响应的结果是json数据，那么加上@responsebody后，就会直接返回json数据。

例如：

```java
@RequestMapping("/login.do")
@ResponseBody
public Object login(String name, String password, HttpSession session) {
    user = userService.checkLogin(name, password);
    session.setAttribute("user", user);
    return new JsonResult(user);
}
```

### @RequestParam

@RequestParam注解有两个属性： value、required；

value用来指定要传入值的id名称required用来指示参数是否必须绑定；

例1：

```java
@RequestMapping("/t1_param")
public ModelAndView userCenter(@RequestParam Long uid){

}
```

例2：

```
@RequestMapping("/t2_param")
public ModelAndView userCenter(Long uid){

}
```

例1 必须带有参数,也就是说你直接输入localhost:8080/t1_param 会报错，只能输入localhost:8080/t_rparam1?userId=? 才能执行相应的方法。

例2 可带参数也可不带参数;也就是说输入localhost:8080/t2_param和输入 localhost:8080/t2_param?userId=?都可以正常运行

当然我们也可以设置 @RequestParam 里面的required为false(默认为true 代表必须带参数) 这样t_rparam1就跟t_rparam2是一样的了。

### @RequestHeader

利用@RequestHeader 注解可以把Request请求header部分的值绑定到方法的参数上。

例请求头：

```xml
Accept	
text/html,application/xhtml+xm…plication/xml;q=0.9,*/*;q=0.8
Accept-Encoding	gzip, deflate, br
Accept-Language	zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Connection keep-alive
Host ss0.bdstatic.com
User-Agent Mozilla/5.0 (Windows NT 10.0; …) Gecko/20100101 Firefox/64.0
```

例：

```java
@RequestMapping("/param")
public ModelAndView userCenter(HttpServletRequest request,
                               HttpServletResponse response,
                               @RequestHeader("Accept-Encoding") String encoding){
    //do something
}
```

### @CookieValue

@CookieValue就是把Request header中cookie的值绑定到方法的参数上。

例：

```java
public void getCookieValueTest (@CookieValue("JSESSIONID") String cookie){
	//do something
}
```

### @RestController

@RestController：Spring4之后加入的注解，原来在@Controller中返回json需要@ResponseBody来配合，如果直接用@RestController替代@Controller就不需要再配置@ResponseBody，默认返回json格式。



