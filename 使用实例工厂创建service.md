# 使用实例工厂创建service

## 接口：

```java
public interface UserService {
	void createUser(String user);
}

```

## 实现类1：

```java
@Service
public class UserServiceByDBImpl implements UserService {
	@Override
    public void createUser(String user) {
        System.out.println("create user " + user + " by db");
    }
}
```

## 实现类2：

```java
@Service
public class UserServiceByFileImpl implements UserService{
	@Override
    public void createUser(String user) {
        System.out.println("create user " + user + " by file");
    }
}
```

## controller:

```java
@RestController
@RequestMapping("/user")
public class UserController {
	@Autowired
	@Qualifier("userService")
    private UserService userService;
	 
    @GetMapping("/create")
    String createUser() {
    	userService.createUser("myName");
        return null;
    }
}
```

## 注入：

```xml
<mvc:annotation-driven></mvc:annotation-driven>
<bean id="serviceLocator" class="top.kwrcee.proxy.ServiceLocator"></bean>
<bean id="userServiceFactory" class="top.kwrcee.proxy.UserServiceFactory"></bean>
<!-- 使用实例工厂创建 UserService实例-->
<bean id="userService" 
      factory-bean="userServiceFactory"
      factory-method="createService">
</bean>
<!-- 配置自动扫描的包 -->
	<context:component-scan base-package="top.kwrcee"></context:component-scan>
```

## 实例工厂：

```java
@Component
public class UserServiceFactory {
	@Autowired
	private ServiceLocator serviceLocator;//获取上下文（定位器）
	@Autowired
	HttpSession session;//获取session
	public UserService createService()
    {
		ApplicationContext applicationContext=serviceLocator.getApplicationContext();
		System.out.println("applicationContext:"+applicationContext);
		Map<String, UserService> map = applicationContext.getBeansOfType(UserService.class);
        List<String> keys=new ArrayList<>();
        List<UserService> values=new ArrayList<>();
        System.out.println("UserService map size:"+map.size());
        for (Map.Entry<String, UserService> entry : map.entrySet()) { 
        	keys.add(entry.getKey());
        	values.add(entry.getValue());
        	System.out.println("Key = " + entry.getKey() + ", Value = " + entry.getValue());
          
        }
        if(values.size()==0) {
        	return null;
        }
        //根据session中的getLastAccessedTime 调用不同service
        int num=0;
        //获取上下文中的session
        Map<String, HttpSession> sessionMap=applicationContext.getBeansOfType(HttpSession.class);
        //获取session中getLastAccessedTime
        for (Map.Entry<String, HttpSession> entry : sessionMap.entrySet()) { 
        	num=(int)(entry.getValue().getLastAccessedTime()&1);
        }
        //不同的session 调用不同的service
    	num=num%values.size();
        System.out.println("调用service名称:"+keys.get(num));
        System.out.println("============================");
		return values.get(num);
    }
}
```

## 定位器：

```java
public class ServiceLocator implements ApplicationContextAware {
    private ApplicationContext applicationContext; // Spring应用上下文环境
	@Override
	public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
		this.applicationContext = applicationContext;
		System.out.println("在此处获取上下文");
   }
	public ApplicationContext getApplicationContext() {
        return applicationContext;
	}

}
```

## web.xml配置

```xml
<context-param>  
<param-name>contextConfigLocation</param-name>  
<param-value>classpath:springMVC.xml</param-value>  
</context-param> 
<listener>
<listener-class>org.springframework.web.context.request.RequestContextListener</listener-class>
</listener>
```

## 流程阐述：

##### 	1.首先要想获取容器中接口的所有实现类必须先获取上下文，即实现ApplicationContextAware接口并重写setApplicationContext方法，将ApplicationContext对象保存在定位器中，

注：定位器必须添加@Component或者在spring的配置文件中添加与之对应的<bean/>

##### 	2.创建实例工厂来根据上下文动态创建service，添加实例工厂方法createService，在方法中获取上下文，根据上下文获得UserService的所有实现类保存在map集合中，根据时间随机调用service

##### ​	3.在xml文件中注入UserService











