#  **SpringSecurity** **框架简介**

Spring 是非常流行和成功的 Java 应用开发框架，Spring Security 正是 Spring 家族中的

成员。Spring Security 基于 Spring 框架，提供了一套 Web 应用安全性的完整解决方

案。

正如你可能知道的关于安全方面的两个主要区域是“**认证**”和“**授权**”（或者访问控

制），一般来说，Web 应用的安全性包括**用户认证（Authentication）和用户授权**

**（Authorization）**两个部分，这两点也是 Spring Security 重要核心功能。

（1）用户认证指的是：验证某个用户是否为系统中的合法主体，也就是说用户能否访问

该系统。用户认证一般要求用户提供用户名和密码。系统通过校验用户名和密码来完成认

证过程。**通俗点说就是系统认为用户是否能登录**

（2）用户授权指的是验证某个用户是否有权限执行某个操作。在一个系统中，不同用户

所具有的权限是不同的。比如对一个文件来说，有的用户只能进行读取，而有的用户可以

进行修改。一般来说，系统会为不同的用户分配不同的角色，而每个角色则对应一系列的

权限。**通俗点讲就是系统判断用户是否有权限去做某些事情。**

--------------------------

“Spring Security 开始于 2003 年年底,““spring 的 acegi 安全系统”。 起因是 Spring

开发者邮件列表中的一个问题,有人提问是否考虑提供一个基于 spring 的安全实现。

Spring Security 以“The Acegi Secutity System for Spring” 的名字始于 2013 年晚些

时候。一个问题提交到 Spring 开发者的邮件列表，询问是否已经有考虑一个机遇 Spring 

的安全性社区实现。那时候 Spring 的社区相对较小（相对现在）。实际上 Spring 自己在

2013 年只是一个存在于 ScourseForge 的项目，这个问题的回答是一个值得研究的领

域，虽然目前时间的缺乏组织了我们对它的探索。

考虑到这一点，一个简单的安全实现建成但是并没有发布。几周后，Spring 社区的其他成

员询问了安全性，这次这个代码被发送给他们。其他几个请求也跟随而来。到 2014 年一

月大约有 20 万人使用了这个代码。这些创业者的人提出一个 SourceForge 项目加入是为

了，这是在 2004 三月正式成立。

在早些时候，这个项目没有任何自己的验证模块，身份验证过程依赖于容器管理的安全性

和 Acegi 安全性。而不是专注于授权。开始的时候这很适合，但是越来越多的用户请求额

外的容器支持。容器特定的认证领域接口的基本限制变得清晰。还有一个相关的问题增加

新的容器的路径，这是最终用户的困惑和错误配置的常见问题。

Acegi 安全特定的认证服务介绍。大约一年后，Acegi 安全正式成为了 Spring 框架的子项

目。1.0.0 最终版本是出版于 2006 -在超过两年半的大量生产的软件项目和数以百计的改

进和积极利用社区的贡献。

Acegi 安全 2007 年底正式成为了 Spring 组合项目，更名为"Spring Security"。

----------------------

**同款产品对比**： **Shiro**

Apache 旗下的轻量级权限控制框架。

特点：

*  轻量级。Shiro 主张的理念是把复杂的事情变简单。针对对性能有更高要求

的互联网应用有更好表现。

* 通用性。
  * 好处：不局限于 Web 环境，可以脱离 Web 环境使用。
  * 缺陷：在 Web 环境下一些特定的需求需要手动编写代码定制。

Spring Security 是 Spring 家族中的一个安全管理框架，实际上，在 Spring Boot 出现之

前，Spring Security 就已经发展了多年了，但是使用的并不多，安全管理这个领域，一直

是 Shiro 的天下。

相对于 Shiro，在 SSM 中整合 Spring Security 都是比较麻烦的操作，所以，Spring 

Security 虽然功能比 Shiro 强大，但是使用反而没有 Shiro 多（Shiro 虽然功能没有

Spring Security 多，但是对于大部分项目而言，Shiro 也够用了）。

自从有了 Spring Boot 之后，Spring Boot 对于 Spring Security 提供了自动化配置方

案，可以使用更少的配置来使用 Spring Security。

因此，一般来说，常见的安全管理技术栈的组合是这样的：

• SSM + Shiro

• Spring Boot/Spring Cloud + Spring Security

**以上只是一个推荐的组合而已，如果单纯从技术上来说，无论怎么组合，都是可以运行**

**的。**

# SpringSecurity入门案例

* 新建SpringBoot项目：勾选web场景与Security场景

![image-20220713144057748](springsecurity.assets/image-20220713144057748.png)

* 编写controller

```java
@RestController
@RequestMapping("/security")
public class SecurityController {
    @GetMapping
    public String helloSecurity(){
        return "hello security";
    }
}
```

* 启动项目访问：localhost:8080/security

* 会发现，需要登录，此时我们可以通过 用户名：user 和启动项目控制台给我们的密码进行登录

![image-20220713163512211](springsecurity.assets/image-20220713163512211.png)

* 登录后，即可正常访问我们的controller	

# **SpringSecurity** **基本原理**

SpringSecurity 本质是一个过滤器链：

从启动是可以获取到过滤器链：

```java
org.springframework.security.web.context.request.async.WebAsyncManagerIntegrationFil
ter
    
org.springframework.security.web.context.SecurityContextPersistenceFilter 

org.springframework.security.web.header.HeaderWriterFilter

org.springframework.security.web.csrf.CsrfFilter

org.springframework.security.web.authentication.logout.LogoutFilter 

org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter 

org.springframework.security.web.authentication.ui.DefaultLoginPageGeneratingFilter 

org.springframework.security.web.authentication.ui.DefaultLogoutPageGeneratingFilter

org.springframework.security.web.savedrequest.RequestCacheAwareFilter

org.springframework.security.web.servletapi.SecurityContextHolderAwareRequestFilter

org.springframework.security.web.authentication.AnonymousAuthenticationFilter 

org.springframework.security.web.session.SessionManagementFilter 

org.springframework.security.web.access.ExceptionTranslationFilter 

org.springframework.security.web.access.intercept.FilterSecurityInterceptor
```

# 两个重要的接口

## **PasswordEncoder** **接口**

* 用于密码加密

* BCryptPasswordEncoder 是 Spring Security 官方推荐的密码解析器，平时多使用这个解析

  器。

```java
class SpringSecurityApplicationTests {
    @Test
    void contextLoads() {
        BCryptPasswordEncoder bCryptPasswordEncoder = new BCryptPasswordEncoder();
        String encode = bCryptPasswordEncoder.encode("123");
       	System.out.println(encode);
        //$2a$10$KMWmuTWfoqR9YY/g/lfPLeEllflrLR./3Jr2bGwmoZZyOhgLTXMhS
    }
}
```



# **SpringSecurity Web** **权限方案**

## **在** **application.properties设置登录系统的账号、密码**

![image-20220713185237928](springsecurity.assets/image-20220713185237928.png)

* 此时登录需要配置文件中的用户名和密码

## 使用配置类设置登录系统的账号、密码

* 创建一个配置类继承于WebSecurityConfigurerAdapter的配置类，重写configure(); 设置用户名密码，角色

```java
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Autowired
    private PasswordEncoder passwordEncoder;

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        String password = passwordEncoder.encode("abc123");
        auth.inMemoryAuthentication().withUser("shy").password(password).roles("admin");
    }

    @Bean
    public PasswordEncoder getPasswordEncoder(){
        return new BCryptPasswordEncoder();
    }
}
```

## **编写类实现接口**设置登录系统的账号、密码

* 创建UserDetailService的实现类，实现loadUserByUsername(String s);

```java
@Service
public class MyUserDetailsService implements UserDetailsService {
    @Autowired
    private BCryptPasswordEncoder passwordEncoder;

    @Override
    public UserDetails loadUserByUsername(String s) throws UsernameNotFoundException {
        List<GrantedAuthority> auths =
                AuthorityUtils.commaSeparatedStringToAuthorityList("role");
        return new User("shy",passwordEncoder.encode("abc123"),auths);
    }
}
```

* 完成配置类

```java
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Autowired
    private PasswordEncoder passwordEncoder;
    @Autowired
    private UserDetailsService userDetailsService;

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.userDetailsService(userDetailsService).passwordEncoder(passwordEncoder);
    }

    @Bean
    public PasswordEncoder getPasswordEncoder(){
        return new BCryptPasswordEncoder();
    }
}
```

* 进行测试

--------------------------------

* 使用MybatisPlus整合完成简单的用户认证过程

* 新建mysql数据库 springsecurity

```mysql
create database springsecurity  character set 'utf8';

create table user(
id int primary key auto_increment,
username varchar(20),
password varchar(20)
);

insert into user(username,password)
values
("shy","abc123");
```

* 增加相关依赖

```xml
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>

        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.5.1</version>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
```

* 配置数据库相关信息

```properties
spring.datasource.password=abc123
spring.datasource.username=root
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/springsecurity?serverTimezone=GMT%2B8
```

* 编写mapper接口、对应实体类

```java
package com.shy.springsecurity.mapper;

import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.shy.springsecurity.entity.User;
import org.apache.ibatis.annotations.Mapper;

@Mapper
public interface UserMapper extends BaseMapper<User> {
}
```

```java
package com.shy.springsecurity.entity;

import lombok.Data;

@Data
public class User {
    private Integer id;
    private String userName;
    private String password;
}
```

* 完善MyUserDetailService

```java
@Service
public class MyUserDetailsService implements UserDetailsService {
    @Autowired
    private BCryptPasswordEncoder passwordEncoder;
    @Autowired
    private UserMapper userMapper;

    @Override
    public UserDetails loadUserByUsername(String userName) throws UsernameNotFoundException {
        List<GrantedAuthority> auths =
                AuthorityUtils.commaSeparatedStringToAuthorityList("role");
        QueryWrapper<com.shy.springsecurity.entity.User> userQueryWrapper = 
            												new QueryWrapper<>();
        userQueryWrapper.eq("username",userName);
        //使用MybatisPlus和userName查询数据库中对应的字段
        com.shy.springsecurity.entity.User user = userMapper.selectOne(userQueryWrapper);
        //如果返回null，我们只需要抛出异常即可
        if(user == null){
            throw new UsernameNotFoundException("找不到对应用户");
        }
        return new 		     User(user.getUserName(),passwordEncoder.encode(user.getPassword()),auths);
    }
}
```

* 进行测试