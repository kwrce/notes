# spring 注解学习

## 1.什么是注解

Annontation是Java5开始引入的新特征，中文叫注解。它提供了一种安全的类似注释的机制，用来将任何的信息或元数据（metadata）与程序元素（类、方法、成员变量等）进行关联。为程序的元素（类、方法、成员变量）加上更直观更明了的说明，这些说明信息是与程序的业务逻辑无关，并且供指定的工具或框架使用。	

注解本身是*没有功能*的，就和xml一样，注解和xml一样是一种元数据，所谓元数据就是解释数据的数据，俗称配置，Java注解是附加在代码中的一些元信息，用于一些工具在编译、运行时进行解析和使用，起到说明、配置的功能。注解不会也不能影响代码的实际逻辑，仅仅起到辅助性的作用。包含在 java.lang.annotation 包中。

## 2.注解的用处

a、生成文档。这是最常见的，也是java 最早提供的注解。常用的有@param @return 等
b、跟踪代码依赖性，实现替代配置文件功能。比如Dagger 2依赖注入，未来java开发，将大量注解配置，具有很大用处;
c、在编译时进行格式检查。如@override 放在方法前，如果你这个方法并不是覆盖了超类方法，则编译时就能检查出。

## 3.注解的原理

注解本质是一个继承了Annotation的特殊接口，其具体实现类是Java运行时生成的动态代理类。而我们通过反射获取注解时，返回的是Java运行时生成的动态代理对象$Proxy1。通过代理对象调用自定义注解（接口）的方法，会最终调用AnnotationInvocationHandler的invoke方法。该方法会从memberValues这个Map中索引出对应的值。而memberValues的来源是Java常量池。

## 4.如何定义注解

**常用的4种元注解：**

```java
java.lang.annotation//提供了四种元注解，专门注解其他的注解（在自定义注解的时候，需要使用到元注解）：
@Target –注解用于什么地方，默认值为任何元素，表示该注解用于什么地方。可用的ElementType指定参数  
  ● ElementType.CONSTRUCTOR:用于描述构造器
  ● ElementType.FIELD:成员变量、对象、属性（包括enum实例）
  ● ElementType.LOCAL_VARIABLE:用于描述局部变量
  ● ElementType.METHOD:用于描述方法
  ● ElementType.PACKAGE:用于描述包
  ● ElementType.PARAMETER:用于描述参数
  ● ElementType.TYPE:用于描述类、接口(包括注解类型) 或enum声明
 
 
@Retention –什么时候使用该注解，即注解的生命周期，使用RetentionPolicy来指定
  ●   RetentionPolicy.SOURCE : 在编译阶段丢弃。这些注解在编译结束之后就不再有任何意义，所以它们        不会写入字节码。@Override, @SuppressWarnings都属于这类注解。
  ●   RetentionPolicy.CLASS : 在类加载的时候丢弃。在字节码文件的处理中有用。注解默认使用这种方式
  ●   RetentionPolicy.RUNTIME : 始终不会丢弃，运行期也保留该注解，因此可以使用反射机制读取该注解的信息。我们自定义的注解通常使用这种方式。
    
 
@Documented –注解是否将包含在JavaDoc中
 
@Inherited – 是否允许子类继承该注解
    @Inherited 元注解是一个标记注解，@Inherited阐述了某个被标注的类型是被继承的。如果一个使用了@Inherited修饰的annotation类型被用于一个class，则这个annotation将被用于该class的子类。
```

**自定义注解的规则：**

```java
自定义注解类编写的一些规则:
  1. Annotation型定义为@interface, 所有的Annotation会自动继承java.lang.Annotation这一接口,并且不能再去继承别的类或是接口.
  2. 参数成员只能用public或默认(default)这两个访问权修饰
  3. 参数成员只能用基本类型byte,short,char,int,long,float,double,boolean八种基本数据类型和String、Enum、Class、annotations等数据类型,以及这一些类型的数组.
  4. 要获取类方法和字段的注解信息，必须通过Java的反射技术来获取 Annotation对象,因为你除此之外没有别的获取注解对象的方法
  5. 注解也可以没有定义成员
PS:自定义注解需要使用到元注解
```













