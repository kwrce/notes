# Choerodon学习手册

## 1.开发环境搭建

### 开发工具

- Git
- JDK 1.8.0 及以上
- maven 3.3 及以上
- Docker for Windows
- IDE
- Mysql
- Redis

### Git 安装

```bash
# 请将下面命令按实际情况进行执行
git config --global user.name "Your Name"
git config --global user.email "Your Email"
```

### Java 安装

1. 在 [Oracle 官网](https://www.oracle.com/technetwork/cn/java/javase/downloads/index.html) 下载对应平台的 JDK 1.8.0 以上的环境。
2. 本地执行安装文件，安装 JDK 环境。
3. Win 在环境变量系统变量中 配置环境变量 `JAVA_HOME` 指向JDK安装目录，并将 path 配置 JDK 的环境变量 指向 JDK 安装目录下 `JDK/bin`，
4. 配置完成后打开 git bash 执行 `java` ，有提示则说明环境安装成功。(git bash执行会有乱码，cmd则正常)

### Maven 安装

1. 在 [Maven 官网](http://mirror.bit.edu.cn/apache/maven/) 下载对应平台的合适的 maven 版本的压缩包。
2. 本地解压压缩包。
3. Win 在环境变量中系统变量的 path 配置 maven 的环境变量指向 maven 解压目录下的 `/bin` 。
4. 配置完成后打开 git bash 执行 `mvn -v` ，有提示则说明环境安装成功。

### Docker for Windows 安装

1. 在 [Docker for Windows](https://www.docker.com/docker-windows) 下载安装包
2. 本地执行安装文件，安装Docker
3. 启动Docker，然后会提示启用Hyper-V需要重启
4. 重启后Docker会自动启动
5. 打开 git bash 执行 `docker --version` ，有提示则说明环境安装成功。
6. 打开 docker for windows，并在General 中勾选 `Expose daemon on tcp://localhost:2375 without TLS`

> **注意要使用Docker for Windows，机器必须开启虚拟化支持，部分机型默认禁用，需进入bios进行设置

### IDE 安装

这里以idea 为例

1. 在IDEA官网下载安装包
2. 本地执行安装文件，安装IDEA
3. 菜单栏File > Setting打开设置， Editor > Code Style > Line separator (for new lines): Unix and OS X (n)
4. 确保idea使用utf-8编码
5. 安装Docker插件。在File-Settings-Plugins中，搜索Docker integration，点击Install安装，并重启软件加载插件
6. IDEA中配置Docker，在File-Settings-Build，Execution，Deployment-Clouds中，点击加号新建，会自动读取docker信息，直接保存即可

### 其他软件安装

除了基本的软件之外，其他基础软件可以通过官网下载安装包，也可以通过docker 启动镜像。Choerodon 建议通过docker 启动。

1. 在本地创建docker-compose的运行路径
2. 编写docker-compose.yaml 文件
3. 打开git bash 执行`docker-compose up -d`
4. 执行`docker ps` 或`docker-compose ps` 查看容器是否启动

这里提供一份默认的mysql 配置和docker-compose.yaml 配置。

```bash
# mysql_db.cnf
[mysqld]
lower_case_table_names=1
character_set_server=utf8
max_connections=500
```

```sql
/** init_user.sql */
CREATE USER 'choerodon'@'%' IDENTIFIED BY "123456";
CREATE DATABASE IF NOT EXISTS iam_service DEFAULT CHARACTER SET utf8;
CREATE DATABASE IF NOT EXISTS manager_service DEFAULT CHARACTER SET utf8;
CREATE DATABASE IF NOT EXISTS asgard_service DEFAULT CHARACTER SET utf8;
CREATE DATABASE IF NOT EXISTS notify_service DEFAULT CHARACTER SET utf8;
GRANT ALL PRIVILEGES ON iam_service.* TO choerodon@'%';\
GRANT ALL PRIVILEGES ON manager_service.* TO choerodon@'%';\
GRANT ALL PRIVILEGES ON asgard_service.* TO choerodon@'%';\
GRANT ALL PRIVILEGES ON notify_service.* TO choerodon@'%';\
FLUSH PRIVILEGES;
```

```yaml
# docker-compose.yaml
version: "3"
services:
  mysql:
    container_name: mysql
    hostname: mysql
    image: registry.cn-hangzhou.aliyuncs.com/choerodon-tools/mysql:5.7.17
    ports:
    - "3306:3306"
    environment:
      MYSQL_ROOT_PASSWORD: root
    volumes:
    - ./mysql/mysql_data:/var/lib/mysql
    - ./mysql/mysql_db.cnf:/etc/mysql/conf.d/mysql_db.cnf
    - ./mysql/init_user.sql:/docker-entrypoint-initdb.d/init_user.sql
    expose:
    - "3306"
    networks:
    - "c7nNetwork"
  redis:
    container_name: redis
    hostname: redis
    image: redis:4.0.11
    ports:
    - "6379:6379"
    expose:
    - "6379"
    networks:
    - "c7nNetwork"
networks:
  c7nNetwork:
    driver: bridge
```

## 2.DEMO

## 表结构

- `todo_user` 用户表，存储该项目中的用户信息

  | 字段名          | 字段类型        | 字段说明 |
  | --------------- | --------------- | -------- |
  | id              | BIGINT UNSIGNED | 主键     |
  | employee_name   | VARCHAR         | 员工名   |
  | employee_number | VARCHAR         | 员工号   |
  | email           | VARCHAR         | 邮箱     |

- `todo_task` 任务表，存储该项目中所有的任务信息和任务与用户的关系

  | 字段名           | 字段类型        | 字段说明 |
  | ---------------- | --------------- | -------- |
  | id               | BIGINT UNSIGNED | 主键     |
  | employee_id      | BIGINT          | 员工ID   |
  | task_number      | VARCHAR         | 任务编号 |
  | task_description | VARCHAR         | 任务描述 |
  | state            | VARCHAR         | 状态     |

## 创建maven项目

本地新建一个空的 `maven` 项目`choerodon-todo-service`。

```bash
$ mkdir -p choerodon-todo-service
$ cd choerodon-todo-service
```

## 添加项目依赖

创建`pom.xml` 文件。

```bash
$ touch pom.xml
```

修改`pom.xml`。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://maven.apache.org/POM/4.0.0"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <groupId>io.choerodon</groupId>
    <artifactId>choerodon-todo-service</artifactId>
    <version>1.0.0</version>
    <!--choerodon-framework-parent dependency-->
    <parent>
        <groupId>io.choerodon</groupId>
        <artifactId>choerodon-framework-parent</artifactId>
        <version>0.8.0.RELEASE</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <!--choerodon-starters dependency-->
    <properties>
        <choerodon.starters.version>0.7.0.RELEASE</choerodon.starters.version>
    </properties>
    <dependencies>
        <!--spring boot-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-undertow</artifactId>
        </dependency>
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
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>

        <!--spring cloud-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-eureka</artifactId>
        </dependency>

        <!--choerodon-->
        <dependency>
            <groupId>io.choerodon</groupId>
            <artifactId>choerodon-starter-core</artifactId>
            <version>${choerodon.starters.version}</version>
        </dependency>
        <dependency>
            <groupId>io.choerodon</groupId>
            <artifactId>choerodon-starter-oauth-resource</artifactId>
            <version>${choerodon.starters.version}</version>
        </dependency>
        <dependency>
            <groupId>io.choerodon</groupId>
            <artifactId>choerodon-starter-swagger</artifactId>
            <version>${choerodon.starters.version}</version>
        </dependency>

    </dependencies>

    <build>
        <finalName>choerodon-todo-service</finalName>
    </build>

</project>
```

根据子级模块所需jar包添加需要的依赖。

- (必须)choerodon-starter-core，核心工具包。提供了一些基础类用于开发过程中使用。以及主要帮助获取自定义的 `userDetail` 和一些通用的方法。
- (必须)choerodon-starter-oauth-resource，oauth资源服务工具包，主要提供了服务`controller` 的异常统一捕获，并转换成用户语言对应的描述信息，以及配置了服务在接受请求时对jwt token的验证规则。
- choerodon-starter-mybatis-mapper，通用mapper和分页插件集成，扩展多语言、审计字段等功能。

更多`choerodon-starter`的依赖可以参考[choerodon-starters](https://github.com/choerodon/choerodon-starters)。

## 添加默认配置文件

在根目录下创建源码文件夹和资源文件夹。

```bash
$ mkdir -p src/main/java
$ mkdir -p src/main/resources
```

项目采用spring boot 进行管理。需要在子项目中配置默认的配置项。

在`resource`文件夹中创建 `application.yml`, `bootstrap.yml`。

```bash
$ cd src/main/resources
$ touch application.yml
$ touch bootstrap.yml
```

- `bootstrap.yml`: 存放不会通过环境变量替换和必须在bootstrap中指定的变量。包括项目端口，应用名，`config-server`地址等。
- `application.yml`: 存放项目的基础配置，包含默认的线上数据库连接配置，`kafka`配置，注册中心地址等，这些变量可以通过`profile`或者环境变量修改。
- `application-default.yml`: 本地开发配置文件，需要将该文件添加到`.gitignore`。包含本地一些差异化的配置，如数据库连接配置，注册中心地址等。

```yml
# bootstrap.yml
server:
  port: 18080
spring:
  application:
    name: choerodon-todo-service
  cloud:
    config:
      failFast: true
      retry:
        maxAttempts: 6
        multiplier: 1.5
        maxInterval: 2000
      uri: localhost:8010
      enabled: false
management:
  port: 18081
  security:
    enabled: false
security:
  basic:
    enabled: false
# application.yml
eureka:
  instance:
    preferIpAddress: true
    leaseRenewalIntervalInSeconds: 10
    leaseExpirationDurationInSeconds: 30
    metadata-map:
      VERSION: v1
  client:
    serviceUrl:
      defaultZone: http://localhost:8000/eureka/
    registryFetchIntervalSeconds: 10
mybatis:
  mapperLocations: classpath*:/mapper/*.xml
  configuration: # 数据库下划线转驼峰配置
    mapUnderscoreToCamelCase: true
```

编写TodoServiceApplication类

在`src/main/java`中创建TodoServiceApplication。

```bash
$ mkdir -p src/main/java/io/choerodon/todo
$ touch src/main/java/io/choerodon/todo/TodoServiceApplication.java
```

添加`main` 函数。

```java
package io.choerodon.todo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import io.choerodon.resource.annoation.EnableChoerodonResourceServer;

@SpringBootApplication
// 是否允许注册到注册中心，暂时注释掉
//@EnableEurekaClient
@RestController
// 是否开启猪齿鱼资源服务器
// @EnableChoerodonResourceServer
public class TodoServiceApplication {
    public static void main(String[] args) {
        SpringApplication.run(TodoServiceApplication.class, args);
    }

    @GetMapping
    @Permission(level = ResourceLevel.SITE, permissionPublic = true)
    @ApiOperation(value = "demo")
    public ResponseEntity<String> hello() {
        return new ResponseEntity<String>("hello world", HttpStatus.OK);
    }
}
```

启动应用

项目根目录下执行命令。

```bash
$ mvn clean spring-boot:run
```

控制台打印出如下信息，则表示启动成功。

```bash
Started TodoServiceApplication in 20.651 seconds (JVM running for 24.976)
```

此时可以打开浏览器，在浏览器输入：`http://localhost:18081/health`

返回如下信息：

```json
{
status: "UP",
    diskSpace: {
        status: "UP"
    },
    db: {
        status: "UP",
        database: "MySQL",
        hello: 1
    },
    refreshScope: {
        status: "UP"
    },
    hystrix: {
        status: "UP"
    }
}
```

在浏览器输入：`http://localhost:18080/hello`，页面打印 `hello world`。

这样，一个简单的`Spring boot` 应用就已经搭建成功。