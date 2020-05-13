---
layout: post
title: Springboot系列记录（二）——配置文件的读取
date: 2019-07-02 
tag: SpringBoot
---

## 前言
Spring默认使用yml中的配置，但有时候要用传统的xml或properties配置，就需要使用spring-boot-configuration-processor了
首先在Maven配置文件中加载依赖，这样Springboot将会创建读取属性文件的上下文。
```java
       <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <version>2.1.3.RELEASE</version>
        </dependency>
```
有了依赖，可以直接使用**application.properties**文件为我们工作了，这是Springboot的默认文件，它会通过其机制读取到上下文中，这样就可以引用它了。
## 读取配置文件
在使用maven项目中，配置文件会放在**resources**根目录下。
我们的springBoot是用Maven搭建的，所以springBoot的默认配置文件和自定义的配置文件都放在此目录。
springBoot的 默认配置文件为 **application.properties** 或 **application.yml**，这里我们使用 **application.properties**。
首先在**application.properties**中添加我们要读取的数据。
```java
databse.url=jdbc:mysql://localhost:3306/test?useUnicode=true&zeroDateTimeBehavior=convertToNull&autoReconnect=true
database.username=root
database.password=2988
database.driverName=com.mysql.jdbc.Driver
```
## 第一种方式
我们可以通过**@Value**注解，这是Spring就有的，使用**${...}**占位符来读取配置在属性文件中的内容，既可以加在属性也可以加在方法上，如果有很多我们就要写很多，比较麻烦，于是就有第二种方式
```java
package com.example.demo1.config;
 
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;
 
@Component
public class DataBaseProperties {
 
    @Value("${database.url}")
    private String url = null;
    @Value("${database.username}")
    private String username = null;
 
    private String password = null;
 
    private String driverName = null;
 
    public String getUrl() {
        return url;
    }
 
    public void setUrl(String url) {
        System.out.println(url);
        this.url = url;
    }
 
    public String getUsername() {
        return username;
    }
 
    public void setUsername(String username) {
        System.out.println(username);
        this.username = username;
    }
 
    public String getPassword() {
        return password;
    }
 
    @Value("${database.password}")
    public void setPassword(String password) {
        System.out.println(password);
        this.password = password;
    }
 
    public String getDriverName() {
        return driverName;
    }
 
    @Value("${database.driverName}")
    public void setDriverName(String driverName) {
        System.out.println(driverName);
        this.driverName = driverName;
    }
}
```
## 第二种方式：
使用**@ConfigurationProperties**
通过注解**@ConfigurationProperties(prefix="配置文件中的key的前缀")**可以将配置文件中的配置自动与实体进行映射，默认从全局配置文件中获取值。比如在yml中有如下配置信息
```java
person:  
  name: zhangsan  
  age: 18
```
```java
@Controller
@ConfigurationProperties(prefix="person")
public class QuickStartController {
private String name;
private Integer age;
@RequestMapping("/quick") 
@ResponseBody
publicStringquick(){
return"springboot 访问成功! name="+name+",age="+age;    }
public void setName(Stringname) {this.name=name;    }
public void setAge(Integerage) {this.age=age;    }}
```


    @ConfigurationProperties("database")这里的字符串database会和类中的属性名称组成全限定名去配置文件中查找