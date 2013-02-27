---
layout: post
title: "JavaEE学习之Spring技术"
date: 2013-02-27 17:19
comments: true
categories: [javaEE, note]
---

Spring框架是一种从实际开发中抽取出来的框架，提供了一种模板的设计哲学，这些模板完成了大量的通用步骤，开发者只需实现特定应用有关的步骤。Spring提供了一种一站式的解决方案，贯穿表现层，业务层和持久层等，以高度的开放性将已有的框架进行整合。

<!-- more -->

## Spring框架使用流程

### 0. 为web应用添加Spring支持

将Spring项目下的dist路径下的全部jar包和spring-framework-v复制到WEB-INF\lib路径下，即可在web应用中使用Spring框架。

### 1. 编写主程序初始化Spring容器

    package hou;

    import PersonService;
    import org.springframework.context.ApplicationContext;
    import org.springframework.context.support.ClassPathXmlApplicationContext;

    public class SpringTest
    {
	    public static void main(String[] args)
	    {
		    ApplicationContext ctx = new ClassPathXmlApplicationContext("bean.xml");
		    System.out.println(ctx);
		    PersonService p = ctx.getBean("personService" , PersonService.class);
		    p.info();	
	    }
    }

### 2. 编写被Spring容器管理的bean

    public class PersonService
    {
	    private String name;
	
	    public void setName(String name)
	    {
		    this.name = name;
	    }
	    public void info()
	    {
		    System.out.println("名字"+ name);
	    }
    }

### 3. 将bean类部署到Spring容器中，通过配置文件

    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	    xmlns="http://www.springframework.org/schema/beans"
	    xsi:schemaLocation="http://www.springframework.org/schema/beans
	    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">
	    <!-- 将PersonService类部署成Spring容器中的Bean  -->
	    <bean id="personService" class="PersonService">
		    <property name="name" value="wawa"/>
	    </bean>
    </beans>

通过以上的简单例子，可以发现Spring容器的一个作用，即IoC（控制反转），就是说Spring容器可以创建bean对象，并通过配置文件设置实例的属性值。这是一种通过Spring容器注入设置实例属性值的方法。

## Spring框架的核心机制

Spring实现IoC的核心机制是依赖注入。通常当一个调用者的java实例需要另一个被调用者的java实例时，需要调用者来创建被调用者的实例; 而在依赖注入中，创建工作由Spring容器执行，而后注入到调用者中，实现动态管理各个实例。

## 参考
[轻量级Java EE企业应用实战](http://book.douban.com/subject/6002664/)
