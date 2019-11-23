# spring-cloud-config



## 1. 概况

### SpringCloud 统一配置中心

如果微服务架构中没有使用统一配置中心时，所存在的问题： 

- 配置文件分散在各个项目里，不方便维护 
- 配置内容安全与权限，实际开发中，开发人员是不知道线上环境的配置的 
- 更新配置后，项目需要重启 

>  Spring Cloud Config就是我们通常意义上的配置中心。Spring Cloud Config-把应用原本放在本地文件的配置抽取出来放在中心服务器，本质是配置信息从本地迁移到云端。从而能够提供更好的管理、发布能力。         
>
> Spring Cloud Config分服务端和客户端，服务端负责将git（svn）中存储的配置文件发布成REST接口，客户端可以从服务端REST接口获取配置。 



![20190602112719252](assets\20190602112719252.png)

## 2. Config Server

### 版本 

springboot <version>2.2.1.RELEASE</version>

springcloud <spring-cloud.version>Hoxton.RC2</spring-cloud.version>

### 添加依赖

```xml
<dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-config-server</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
```



### 在启动类上加注解

```java 
@EnableDiscoveryClient //注册到Eureka
@EnableConfigServer    //统一配置中心服务
```



### 创建Git仓库添加配置文件

![1574481123935](assets\1574481123935.png)



### 修改配置文件

```yaml
server:
  port: 7777
spring:
  application:
    name: config-server
  cloud:
    config:
      server:
        git:
          uri: https://github.com/gx-russel/spring-cloud-config #公开仓库的可以不写账号密码
          basedir: D:/IdeaProjects/config/basedir #本地存放路径 
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka

```


### 启动项目查看配置文件



![1574481839410](assets\1574481839410.png)

<http://localhost:7777/user-test.yml> 

有两种查看方式: 

1. /{name}-{profiles}.yml
2. /{lavel}/{name}-{profiles}.yml

- name:服务名
- profiles:环境
- label:分支(branch)



还可以获取properties和json格式的

![1574481896534](C:\Users\R\AppData\Local\Temp\1574481896534.png)

![1574481916782](assets\1574481916782.png)

注意:不管获取哪个环境的配置文件,都会把默认的配置文件拉到本地然后和其他环境的配置合并,一般把通用的配置放在默认的配置文件中 服务名.yml

在这默认的配置文件也就是user.yml

## 3. Config Client

在仓库添加user-dev.yml

![1574482575504](assets\1574482575504.png)


### 添加依赖

```java
<dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-config-client</artifactId>
        </dependency>
```

### 添加注解

```java
@EnableDiscoveryClient
```



### 修改application.yml

把名字改成 bootstrap.yml 

注意:

- bootstrap.yml优先级比application.yml高,如果项目有用到数据库又没有改成bootstrap.yml启动项目会报错,因为没获取到远端仓库的配置找不到数据库的配置
- 获取配置需要到注册中心获取配置中心的服务,所以要把获取服务中心的地址写到bootstrap.yml中如果写就是端口8761的默认地址,如果没有端口8761的注册中心就会报错找不到注册中心



![1574482390457](C:\Users\R\AppData\Local\Temp\1574482813373.png)

```yaml
spring:
  application:
    name: user #服务名
  cloud:
    config:
      profile: dev #环境
      discovery:
        enabled: true
        service-id: config-server #配置中心服务名
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka/

```

启动项目访问8889可以访问证明用的是配置中心的user-dev.yml配置文件

<http://localhost:8889/> 

![1574482870573](assets\1574482870573.png)





