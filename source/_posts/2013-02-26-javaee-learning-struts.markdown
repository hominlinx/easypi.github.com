---
layout: post
title: "JaveEE学习之Struts2技术"
date: 2013-02-26 15:39
comments: true
categories: [javaEE, note]
---

Struts2是一款优秀的MVC框架，具有一系列的优点使其具有巨大的吸引力。这些优点包括：允许使用普通java对象作为Action，并且降低了与Servlet API的耦合，更容易测试;支持更多的视图技术;提供良好的可扩展性，更强大的输入校验功能;整合Ajax支持等。本文介绍简单的Struts2应用，来对其进行了解。

<!-- more -->

## Struts2开发应用流程

这里用一个Struts2开发简单登录处理的web应用来介绍Struts2的开发应用流程

### 0. 为web应用添加Struts2支持

将下载的Struts2中lib文件夹下的 `commons-fileupload-v.jar, commons-io-v.jar, freemaker-v.jar, javassit-v.jar, ognl-v.jar, struts2-core-v.jar, xwork-core-v.jar`等必须的支持文件（其中`-v`表示版本号，根据下载不同会有所不同）复制到web应用的`WEB-INF\lib`路径下，如需其他Struts2的支持，复制相应jar文件即可。

### 1. 编辑web应用的web.xml配置文件，配置Struts2的核心filter，并使其拦截相关请求。

在web.xml配置文件中增加以下内容：

	<filter>
		<filter-name>struts2</filter-name>
		<filter-class>org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter</filter-class>
	</filter>

	<filter-mapping>
		<filter-name>struts2</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>

### 2. 编写处理表单请求的JSP页面来发送POST方式请求，如果是GET方式请求，则此步不进行处理。

在本登录应用中，需要处理表单请求，编写login.jsp如下

    <%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>
    <%@taglib prefix="s" uri="/struts-tags"%>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=GBK">
    <title><s:text name="loginPage"/></title>
    </head>
    <body>
    <s:form action="login">
	    <s:textfield name="username" key="user"/>
	    <s:textfield name="password" key="pass"/>
	    <s:submit key="login"/>
    </s:form>
    </body>
    </html>

在本jsp的编写中声明使用了struts2的标签库，并使用该标签库来编写表单请求代码。

### 3. 定义处理用户请求的Action类

在上述的jsp页面中指定了表单的action为login，因此需要实现一个java类来处理该请求动作。该类通常继承与ActionSupport基类，其代码如下：

    import com.opensymphony.xwork2.ActionContext;
    import com.opensymphony.xwork2.ActionSupport;

    public class LoginAction extends ActionSupport
    {
	    private String username;
	    private String password;
	
	    public String getUsername()
	    {
		    return username;
	    }
	    public void setUsername(String username)
	    {
		    this.username = username;
	    }
	
	    public String getPassword()
	    {
		    return password;
	    }
	    public void setPassword(String password)
	    {
		    this.password = password;
	    }

	    public String execute() throws Exception
	    {
		    if (getUsername().equals("easypi.github.com")
			    && getPassword().equals("easypi") )
		    {
			    ActionContext.getContext().getSession()
				    .put("user" , getUsername());
		    return SUCCESS;
		    }
		    else
		    {
			    return ERROR;
		    }
	    }
    }

该类实现对登录的判断，如果用户名为`easypi.github.com`，密码为`easypi`则登录成功，否则失败。

### 4. 配置Action以及处理结果和物理视图资源之间的对应关系

在定义了请求并实现了处理请求action的类之后，需要将请求与action对应起来，用来实现MVC模型。建立struts.xml放置在`WEB-INF\classes`路径下，使用该文件实现web应用的配置。该文件代码如下：

    <?xml version="1.0" encoding="GBK"?>
    <!DOCTYPE struts PUBLIC 	"-//Apache Software Foundation//DTD Struts Configuration 2.1.7//EN"
	    "http://struts.apache.org/dtds/struts-2.1.7.dtd">
    <struts>
	    <package name="hou" extends="struts-default">
		    <action name="login" class="LoginAction">	
			    <result name="input">/login.jsp</result>
			    <result name="error">/error.jsp</result>
			    <result name="success">/welcome.jsp</result>
		    </action>
	    </package>
    </struts>

其中，将action的login请求使用LoginAction类来进行处理，并可以配置返回结果与物理视图资源之间的对应，如登录成功则进入welcome.jsp页面。

### 5. 实现物理视图资源

使用Struts2开发的最后一步就是实现需要的视图资源，例如welcome.jsp, error.jsp等页面。至此则完成了使用Struts2开发web应用的全部流程，也实现了一次完整的请求响应流程。

## Struts2 请求响应流程

Struts2的开发流程实际是根据请求响应流程而来的，一次请求响应流程如下。

![Struts2 请求响应流程](http://ww1.sinaimg.cn/large/a74ecc4cjw1e26usgyzmaj.jpg)

其中StrutsPrepareAndExecuteFilter称为核心控制器，xxxAction称为业务控制器。通过两级控制器实现解耦，实现xxxAction只处理返回结果，不与物理视图相连，方便代码更改。

