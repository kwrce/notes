# Hap中实现单点登录的操作

### 1.在 DemoParent/core/overlays/com.hand.hap-3.4.1-RELEASE/WEB-INF/classes/spring/applicationContext-security.xml  中配置security ,将casSecurity.xml配置进去

```xml
<!-- 根据项目需求选择使用CAS或标准登录方式  -->
<!-- <beans:import resource="casSecurity.xml"/> -->
<beans:import resource="casSecurity.xml"/>
<!--<beans:import resource="standardSecurity.xml"/>-->
```

### 2.DemoParent\core\overlays\com.hand.hap-3.4.1-RELEASE\WEB-INF\classes\config.properties中配置cas

```properties
#CAS
cas.service=http://localhost:8080/hap/login/cas
cas.ssoserver.loginurl=https://login.hand-china.com/sso/login
cas.ssoserver.url=https://login.hand-china.com/sso
cas.ssoserver.logouturl=https://login.hand-china.com/sso/logout?service=http://localhost:8080/hap
```

### 3.配置tomcat

![tomcat_conf](D:\git\hapDemo\DemoParent\md_img\tomcat_conf_1.png)

![tomcat_conf_2](D:\git\hapDemo\DemoParent\md_img\tomcat_conf_2.png)

### 4.访问地址

http://localhost:8088/hap