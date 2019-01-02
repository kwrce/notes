# FreeMarker学习

官方文档：http://freemarker.foofun.cn/dgui_template_overallstructure.html

## 1.什么是FreeMarker？

FreeMarker 是一款 *模板引擎*： 即一种基于模板和要改变的数据， 	并用来生成输出文本(HTML网页，电子邮件，配置文件，源代码等)的通用工具。 	它不是面向最终用户的，而是一个Java类库，是一款程序员可以嵌入他们所开发产品的组件。

模板编写为FreeMarker Template Language (FTL)。它是简单的，专用的语言，     *不是* 像PHP那样成熟的编程语言。 	那就意味着要准备数据在真实编程语言中来显示，比如数据库查询和业务运算， 	之后模板显示已经准备好的数据。在模板中，你可以专注于如何展现数据， 	而在模板之外可以专注于要展示什么数据。

## 2.模板 + 数据模型 = 输出

```html
<html>
<head>
  <title>Welcome!</title>
</head>
<body>
  <h1>Welcome ${user}!</h1>
  <p>Our latest product:
  <a href="${latestProduct.url}">${latestProduct.name}</a>!
</body>
</html>
```

模板文件存放在Web服务器上，就像通常存放静态HTML页面那样。当有人来访问这个页面，FreeMarker将会介入执行，然后动态转换模板，用最新的数据内容替换模板中`${*...*}` 的部分，之后将结果发送到访问者的Web浏览器中。

总的来说，模板和数据模型是FreeMarker来生成输出(比如第一个展示的HTML)所必须的：模板 + 数据模型 = 输出

## 3.数据模型一览

```xml
(root)
  |
  +- animals
  |   |
  |   +- mouse
  |   |   |   
  |   |   +- size = "small"
  |   |   |   
  |   |   +- price = 50
  |   |
  |   +- elephant
  |   |   |   
  |   |   +- size = "large"
  |   |   |   
  |   |   +- price = 5000
  |   |
  |   +- python
  |       |   
  |       +- size = "medium"
  |       |   
  |       +- price = 4999
  |
  +- message = "It is a test"
  |
  +- misc
      |
      +- foo = "Something"
```

上图中的变量扮演目录的角色(比如`root`, `animals`, `mouse`,`elephant`, `python`,  `misc`) 被称为 **hashes** (哈希表或哈希，译者注)。哈希表存储其他变量(被称为*子变量*)，它们可以通过名称来查找(比如 "animals","mouse" 或 "price")。
存储单值的变量(`size`, `price`, `message` 和 `foo`) 称为**scalars** (标量，译者注)。
如果要在模板中使用子变量，那应该从根root开始指定它的路径，每级之间用点来分隔开。要访问 `mouse` 的 `price` ，要从root开始，首先进入到 `animals` ，之后访问`mouse` ，最后访问 `price` 。就可以这样来写`animals.mouse.price`。

另外一种很重要的变量是 **sequences** (序列，译者注)。它们像哈希表那样存储子变量，但是子变量没有名字，它们只是列表中的项。
比如，在下面这个数据模型中， `animals` 和`misc.fruits` 就是序列：

```
root)
  |
  +- animals
  |   |
  |   +- (1st)
  |   |   |
  |   |   +- name = "mouse"
  |   |   |
  |   |   +- size = "small"
  |   |   |
  |   |   +- price = 50
  |   |
  |   +- (2nd)
  |   |   |
  |   |   +- name = "elephant"
  |   |   |
  |   |   +- size = "large"
  |   |   |
  |   |   +- price = 5000
  |   |
  |   +- (3rd)
  |       |
  |       +- name = "python"
  |       |
  |       +- size = "medium"
  |       |
  |       +- price = 4999
  |
  +- misc
      |
      +- fruits
          |
          +- (1st) = "orange"
          |
          +- (2nd) = "banana"
```

要访问序列的子变量，可以使用方括号形式的数字索引下标。索引下标从0开始(从0开始也是程序员的传统)，那么第一项的索引就是0，第二项的索引就是1等等。要得到第一个动物的名称的话，可以这么来写代码 `animals[0].name`。要得到 `misc.fruits` 中的第二项(字符串`"banana"`)可以这么来写`misc.fruits[1]`。(实践中，通常按顺序遍历序列，而不用关心索引)

### 标量类型可以分为如下的类别：

- 字符串：就是文本，也就是任意的字符序列，比如上面提到的 			''m'', ''o'', ''u'', ''s'', ''e''。比如 `name`和 `size` 也是字符串。
- 数字：这是数值类型，就像上面的 `price`。在FreeMarker中，字符串 `"50"` 和数字`50` 是两种完全不同的东西。前者是两个字符的序列(这恰好是人们可以读的一个数字)，而后者则是可以在数学运算中直接被使用的数值。
- 日期/时间: 可以是日期-时间格式(存储某一天的日期和时间)，或者是日期(只有日期，没有时间)，或者是时间(只有时间，没有日期)。
- 布尔值：对应着对/错(是/否，开/关等值)类似的值。比如动物可以有一个 `protected` (受保护的，译者注) 的子变量，该变量存储这个动物是否被保护起来的值。

## 4.模板一览



- **${*...*}**：被称为**interpolation **(插值) ，可以动态替换

- **FTL 标签**：这些标签的名字以 `#` 开头，自定义的标签使用@代替#

- **注释**：`<#--` and `-->` ，不会显示在前端

- 其他形式会以文本的形式输出

### 基本指令

#### <#if > 指令
```
<html>
<head>
  <title>Welcome!</title>
</head>
<body>
  <h1>
    Welcome ${user}<#if user == "Big Joe">, our beloved leader</#if>!
  </h1>
  <p>Our latest product:
  <a href="${latestProduct.url}">${latestProduct.name}</a>!
</body>
</html>
```

此时，告诉 FreeMarker，当和 `"Big Joe"` 相同时", our beloved leader" (我们最尊敬的领导，译者注) 才是if条件中那唯一的 `user` 变量的值。通常来讲，如果 `condition` 是false(布尔值)，那么介于 `<#if condition>` 和`</#if>`标签中的内容会被略过。

#### <#else>的使用

```
<#if animals.python.price < animals.elephant.price>
  Pythons are cheaper than elephants today.
<#else>
  Pythons are not cheaper than elephants today.
</#if>
```

注：可以和list结合使用：

```html
<p>Fruits: <#list misc.fruits as fruit>${fruit}<#sep>, <#else>None</#list></p>
```

#### <#elseif>的使用

```html
<#if animals.python.price < animals.elephant.price>
  Pythons are cheaper than elephants today.
<#elseif animals.elephant.price < animals.python.price>
  Elephants are cheaper than pythons today.
<#else>
  Elephants and pythons cost the same today.
</#if>
```

#### list 指令

```
<p>We have these animals:
<table border=1>
  <#list animals as animal>
    <tr><td>${animal.name}<td>${animal.price} Euros
  </#list>
</table>
```

结果：

```html
<p>We have these animals:
<table border=1>
    <tr><td>mouse<td>50 Euros
    <tr><td>elephant<td>5000 Euros
    <tr><td>python<td>4999 Euros
</table>
```

#### <#sep>分隔符

```html
<p>Fruits: <#list misc.fruits as fruit>${fruit}<#sep>, </#list></p>
```

#### 结合使用实例

```
<#list misc.fruits>
  <p>Fruits:
  <ul>
    <#items as fruit>
      <li>${fruit}<#sep> and</#sep>
    </#items>
  </ul>
<#else>
  <p>We have no fruits.
</#list>
```

#### include 指令

```html
<html>
<head>
  <title>Test page</title>
</head>
<body>
  <h1>Test page</h1>
  <p>Blah blah...
  <#include "/copyright_footer.html">
</body>
</html>
```

### 使用内建函数

内建函数很像子变量(如果了解Java术语的话，也可以说像方法，它们并不是数据模型中的东西，是 FreeMarker 在数值上添加的。为了清晰子变量是哪部分，使用 `?`(问号)代替 `.`(点)来访问它们。常用内建函数的示例：

- `user?html` 给出 `user` 的HTML转义版本，比如 `&` 会由 `&amp;` 来代替。
- `user?upper_case` 给出 `user` 值的大写版本 (比如 "JOHN DOE" 来替代 "John Doe")
- `animal.name?cap_first` 给出 `animal.name`的首字母大写版本(比如 "Mouse" 来替代 "mouse")
- `user?length` 给出 `user` 值中*字符*的数量(对于 "John Doe" 来说就是8)
- `animals?size` 给出 `animals` 序列中 *项目* 的个数(我们示例数据模型中是3个)
- 如果在 `<#list animals as animal>` 和对应的`</#list>` 标签中：
  - `animal?index` 给出了在 `animals`中基于0开始的 `animal`的索引值
  - `animal?counter` 也像 `index`，但是给出的是基于1的索引值
  - `animal?item_parity` 基于当前计数的奇偶性，给出字符串     "odd" 或 "even"。在给不同行着色时非常有用，比如在           `<td class="${animal?item_parity}Row">`中。
  

一些内建函数需要参数来指定行为，比如：
- `animal.protected?string("Y", "N")` 基于`animal.protected` 的布尔值来返回字符串 "Y" 或 "N"。
- `animal?item_cycle('lightRow','darkRow')` 是之前介绍的`item_parity` 更为常用的变体形式。
- `fruits?join(", ")` 通过连接所有项，将列表转换为字符串， 在每个项之间插入参数分隔符(比如 "orange,banana")
- `user?starts_with("J")` 根据 `user`的首字母是否是 "J" 返回布尔值true或false。

#### 处理不存在的变量

不论在哪里引用变量，都可以指定一个默认值来避免变量丢失这种情况，通过在变量名后面跟着一个 `!`(叹号，译者注)和默认值。就像下面的这个例子，当 `user` 不存在于数据模型时,模板将会将 `user` 的值表示为字符串 `"visitor"`。(当 `user` 存在时，模板就会表现出 `${user}` 的值)：

```html
<h1>Welcome ${user!"visitor"}!</h1>
```

也可以在变量名后面通过放置 `??` 来询问一个变量是否存在。将它和 `if` 指令合并，那么如果 `user` 变量不存在的话将会忽略整个问候的代码段：

```html
<#if user??><h1>Welcome ${user}!</h1></#if>
```

关于多级访问的变量，比如 `animals.python.price`，书写代码：`animals.python.price!0` 当且仅当 `animals.python` 永远存在，而仅仅最后一个子变量 `price` 可能不存在时是正确的(这种情况下我们假设价格是 `0`)。如果 `animals` 或 `python` 不存在，那么模板处理过程将会以"未定义的变量"错误而停止。为了防止这种情况的发生，可以如下这样来编写代码 `(animals.python.price)!0`。这种情况就是说 `animals` 或 `python` 不存在时，表达式的结果是 `0`。对于 `??` 也是同样用来的处理这种逻辑的; 将 `animals.python.price??` 对比 `(animals.python.price)??`来看。

## 5.表达式

快速浏览(备忘单)

这里给已经了解 FreeMarker 的人或有经验的程序员的提个醒：

​    直接指定值
​        字符串： "Foo" 或者 'Foo' 或者 "It's \"quoted\"" 或者 'It\'s "quoted"' 或者 r"C:\raw\string"
​        数字： 123.45

​        布尔值： true， false

​        序列： ["foo", "bar", 123.45]； 值域： 0..9, 0..<10 (或 0..!10), 0..

​        哈希表： {"name":"green mouse", "price":150}

​    检索变量
​        顶层变量： user
​        从哈希表中检索数据： user.name， user["name"]
​        从序列中检索数据： products[5]
​        特殊变量： .main

​    字符串操作
​        插值(或连接)： "Hello ${user}!" (或 "Hello " + user + "!")
​        获取一个字符： name[0]
​        字符串切分： 包含结尾： name[0..4]，不包含结尾： name[0..<5]，基于长度(宽容处理)： name[0..*5]，去除开头： name[5..]
​ 操作
​        连接： users + ["guest"]
​        序列切分：包含结尾： products[20..29]， 不包含结尾： products[20..<30]，基于长度(宽容处理)： products[20..*10]，去除开头： products[20..]

​    哈希表操作
​        连接： passwords + { "joe": "secret42" }

​    算术运算： (x * 1.5 + 10) / 2 - y % 100

​    比较运算： x == y， x != y， x < y， x > y， x >= y， x <= y， x lt y， x lte y， x gt y， x gte y， 等等。。。。。。

​    逻辑操作： !registered && (firstVisit || fromEurope)

​    内建函数： name?upper_case, path?ensure_starts_with('/')

​    方法调用： repeat("What", 3)

​    处理不存在的值：
​        默认值： name!"unknown" 或者 (user.name)!"unknown" 或者 name! 或者 (user.name)!

​        检测不存在的值： name?? 或者 (user.name)??

## 6.创建Configuration 实例

首先，你应该创建一个 `freemarker.template.Configuration` 实例，然后调整它的设置。`Configuration` 实例是存储 FreeMarker 应用级设置的核心部分。同时，它也处理创建和 *缓存* 预解析模板(比如 `Template` 对象)的工作。

```java
Configuration cfg = new Configuration(Configuration.VERSION_2_3_22);
//模板位置
cfg.setDirectoryForTemplateLoading(new File("/where/you/store/templates"));
//设置编码
cfg.setDefaultEncoding("UTF-8");
//处理异常
cfg.setTemplateExceptionHandler(TemplateExceptionHandler.RETHROW_HANDLER);
```

## 7.创建数据模型

```
(root)
  |
  +- user = "Big Joe"
  |
  +- latestProduct
      |
      +- url = "products/greenmouse.html"
      |
      +- name = "green mouse"
```

下面是构建这个数据模型的Java代码片段：

```java
// Create the root hash
Map<String, Object> root = new HashMap<>();
// Put string ``user'' into the root
root.put("user", "Big Joe");
// Create the hash for ``latestProduct''
Map<String, Object> latest = new HashMap<>();
// and put it into the root
root.put("latestProduct", latest);
// put ``url'' and ``name'' into latest
latest.put("url", "products/greenmouse.html");
latest.put("name", "green mouse");
```

将它的实例放入数据模型中，就像下面这样：

```java
Product latestProducts = getLatestProductFromDatabaseOrSomething();
root.put("latestProduct", latestProduct);
```

如果latestProduct 是 Map类型， 模板就可以是相同的，比${latestProduct.name} 在两种情况下都好用。

根root本身也无需是 Map，只要是有 getUser() 和 getLastestProduct() 方法的对象即可。

## 8.获取模板

模板代表了 freemarker.template.Template 实例。典型的做法是从 Configuration 实例中获取一个 Template 实例。无论什么时候你需要一个模板实例， 都可以使用它的 getTemplate 方法来获取。在 之前 设置的目录中的 test.ftl 文件中存储 示例模板，那么就可以这样来做：

```java
Template temp = cfg.getTemplate("test.ftl");
```

当调用这个方法的时候，将会创建一个 test.ftl 的 Template 实例，通过读取 /where/you/store/templates/test.ftl 文件，之后解析(编译)它。Template 实例以解析后的形式存储模板， 而不是以源文件的文本形式。

Configuration 缓存 Template 实例，当再次获得 test.ftl 的时候，它可能再读取和解析模板文件了， 而只是返回第一次的 Template 实例。

## 9.合并模板和数据模型

我们已经知道，数据模型+模板=输出，我们有了一个数据模型 (root) 和一个模板 (temp)， 为了得到输出就需要合并它们。这是由模板的 process 方法完成的。它用数据模型root和 Writer 对象作为参数，然后向 Writer 对象写入产生的内容。 为简单起见，这里我们只做标准的输出：

```java
Writer out = new OutputStreamWriter(System.out);
temp.process(root, out);
```

这会向你的终端输出你在模板开发指南部分的 第一个示例 中看到的内容。

Java I/O 相关注意事项：基于 out 对象，必须保证 out.close() 最后被调用。当 out 对象被打开并将模板的输出写入文件时，这是很电影的做法。其它时候， 比如典型的Web应用程序，那就 不能 关闭 out 对象。FreeMarker 会在模板执行成功后 (也可以在 Configuration 中禁用) 调用 out.flush()，所以不必为此担心。

请注意，一旦获得了 Template 实例， 就能将它和不同的数据模型进行不限次数 (Template实例是无状态的)的合并。此外， 当 Template 实例创建之后 test.ftl 文件才能访问，而不是在调用处理方法时。

## 10.将代码放在一起

这是一个由之前的代码片段组合在一起的源程序文件。 千万不要忘了将 freemarker.jar 放到 CLASSPATH 中。

```java
import freemarker.template.*;
import java.util.*;
import java.io.*;

public class Test {

    public static void main(String[] args) throws Exception {
        
        /* ------------------------------------------------------------------------ */    
        /* You should do this ONLY ONCE in the whole application life-cycle:        */    
    
        /* Create and adjust the configuration singleton */
        Configuration cfg = new Configuration(Configuration.VERSION_2_3_22);
        cfg.setDirectoryForTemplateLoading(new File("/where/you/store/templates"));
        cfg.setDefaultEncoding("UTF-8");
        cfg.setTemplateExceptionHandler(TemplateExceptionHandler.RETHROW);

        /* ------------------------------------------------------------------------ */    
        /* You usually do these for MULTIPLE TIMES in the application life-cycle:   */    

        /* Create a data-model */
        Map root = new HashMap();
        root.put("user", "Big Joe");
        Map latest = new HashMap();
        root.put("latestProduct", latest);
        latest.put("url", "products/greenmouse.html");
        latest.put("name", "green mouse");

        /* Get the template (uses cache internally) */
        Template temp = cfg.getTemplate("test.ftl");

        /* Merge data-model with template */
        Writer out = new OutputStreamWriter(System.out);
        temp.process(root, out);
        // Note: Depending on what `out` is, you may need to call `out.close()`.
        // This is usually the case for file output, but not for servlet output.
    }
}
```











