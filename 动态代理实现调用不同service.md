# 动态代理实现调用不同service

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
	@Autowired  
	private HttpSession session; 
	@Override
    public void createUser(String user) {
        System.out.println("create user " + user + " by file");
    }
}
```

## 代理类：

```java
public class Dynamicproxy implements InvocationHandler{
	//这就是我们代理的真实对象
		private Object subject;
		
		
		//问题 ： 在此使用注入会提示空指针异常
//		@Autowired
//		private UserService userServiceByFileImpl;
		//构造方法，给我们要代理的真实对象赋值
		public Dynamicproxy(Object subject){
			this.subject = subject;
		}
		//应该通过接口获取接口的所有实现类
		@Override
		public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
			//在代理真实对象前我们可以添加一些自己的操作
			System.out.println("before invoking");
			System.out.println("Method:"+method);
			Long lastTime=((ServletRequestAttributes)RequestContextHolder.getRequestAttributes()).getRequest().getSession().getLastAccessedTime();
			if(lastTime%2==0) {
				System.out.println("调用代理的实现方法");
				method.invoke(new UserServiceByFileImpl(), args);
			}
			else
				//当代理对象调用真实对象的方法时，其会自动的跳转到代理对象关联的handler对象的invoke方法来进行调用
				method.invoke(subject, args);
			
			//在代理真实对象后我们可以添加一些自己的操作
			System.out.println("after invoking");
			return null;
		}
}
```

## controller:

```java
@RestController
@RequestMapping("/user")
public class UserController {
	@Resource(name="userServiceByDBImpl")
    private UserService userService;
    
    @GetMapping("/create")
    String createUser() {
    	//获取session最后一次访问的时间
    	InvocationHandler handler =new Dynamicproxy(userService);
    	userService=(UserService) Proxy.newProxyInstance(handler.getClass().getClassLoader(), userService.getClass().getInterfaces(), handler);
    	userService.createUser("asdasd");
        return null;
    }
}

```