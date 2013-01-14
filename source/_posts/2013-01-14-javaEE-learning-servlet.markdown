---
layout: post
title: "JaveEE学习之Servlet技术"
date: 2013-01-14 20:17
comments: true
categories: [javaEE, note]
---


JSP的本质就是Servlet，编写JSP将其放入web容器中，web容器将JSP编译为Servlet。 由于作为表现层技术使用时，直接使用Servlet的开发效率低下，因此一般使用如JSP等的表现层技术实现。在MVC规范中，Servlet通常被当作控制器使用，虽然还有很多其他MVC的框架可用，但Servlet作为底层实现，对学习JavaEE技术，了解技术原理是有意义的。本文给出一个简单的符合MVC规范的web应用例子，并使用Servlet作为其中的控制器来学习Servlet的相关技术。

<!-- more -->

## Servlet类介绍

Servlet类是一个继承自`HttpServlet`的特殊类，可以用来响应用户的请求。在编写自己的Servlet时最常使用的方法是`service()`方法，该方法处理客户端的请求。因此，一个Servlet类的典型结构为：
    
    //使用@WebServlet Annotation 配置servlet
    @WebServlet(name="firstServlet", urlPatterns={"/firstServlet"})
    //Servlet类定义
    public class FirstServlet extends HttpServlet
    {
        public void service(HttpServletRequest request,
        HttpServletResponse response) 
        throws ServletException,java.io.IOException 
        {
            //相应用户请求代码
        }
    }

## Servlet类配置

为了使Servlet响应用户请求，还需将Servlet与Web应用配置起来。配置Servlet有两种方法：在web.xml中配置和使用@WebServlet Annotation配置。使用@WebServlet Annotation直接在Java类的编辑中即可实现，不需web.xml，我认为是更好的方法，因此使用该法进行配置。在使用@WebServlet Annotation时，其常用的几个属性如下：

* name:                  指定该Servlet的名称
* urlPatterns/value:     指定该Servlet要处理的url
* initParams：           指定该Servlet的参数
* loadOnStartup:         指定该Servlet是否为load on startup 的Servlet

## Servlet类作为控制器

在MVC模式中，Model通常由JavaBean来充当，用来实现业务逻辑、数据访问逻辑等; View通常由JSP来充当，用来收集用户的请求参数、将应用处理的结果和状态等返回呈现给用户等; Controller则由Servlet来充当，实现类似调度员的作用，即用户请求发送到Servlet，Servlet调用Model来处理用户请求，并将结果调用JSP呈现给用户。MVC模式通过对三者作用的分工，实现应用的解耦，方便多人协同开发同一个项目。

现在给出一个简单的MVC模式的应用，实现登录的验证。首先给出`login.jsp`:

    <%@ page language="java" contentType="text/html; charset=UTF-8"
        pageEncoding="UTF-8"%>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>登录</title>
    </head>
    <body>
    请输入用户名和密码
    <!-- 登录表单，被提交至一个Servlet -->
    <form id="login" method="post" action="login">
    用户名： <input type="text" name="username"/> <br/>
    密码：  <input type="password" name="pass" /> <br/>
    <input type="submit" value="登录" /> <br/>
    </form>
    </body>
    </html>

然后给出控制器Servlet类`LoginServlet.java`:

    import javax.servlet.*;
    import javax.servlet.http.*;
    import javax.servlet.annotation.*;

    import java.sql.*;
    import java.io.*;

    @WebServlet(name="login", urlPatterns={"/login"})

    public class LoginServlet extends HttpServlet {

        public void service(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, java.io.IOException 
        {
                String errMsg="";
                //Servlet不输出响应到客户端，因此必须将请求转发
                RequestDispatcher rd;

                //获取请求参数    
                String username =  request.getParameter("username");
                String pass = request.getParameter("pass");
                PrintStream out = new PrintStream(response.getOutputStream());
                //out.println("name" + username);

                try {
                    //Servlet 本身不处理业务逻辑，调用JavaBean处理用户请求
                    DbDao dd = new DbDao("com.mysql.jdbc.Driver",
                        "jdbc:mysql://localhost:3306/javaee","root","mysql");
                    //查询结果集
                    ResultSet rs = dd.query("select pass from user_table"
                        + "where name = ?", username);
                    if (rs.next()) {
                
                        //用户名和密码匹配
                        if (rs.getString("pass").equals(pass)) {
                            // 获取session对象
                            HttpSession session = request.getSession(true);
                            //设置session属性，跟踪用户会话状态
                            session.setAttribute("name", username);
                            //获取转发对象
                            rd = request.getRequestDispatcher("/welcome.jsp");
                            rd.forward(request, response);
                        }
                        else {
                            //用户名密码不匹配
                            errMsg += "您的用户名密码不符合，请重试";
                        }
                    }
                    else {
                        //用户名不存在时
                        errMsg += "您的用户名不存在，请先注册";
                    }
                
                }
                catch (Exception e) {
                    out.println(errMsg);
                    e.printStackTrace();
                }

                //如果出错，转发到重新登录
                if (errMsg != null && !errMsg.equals("")) {
                    rd = request.getRequestDispatcher("/login.jsp");
                    request.setAttribute("err", errMsg);
                    rd.forward(request, response);
                }

            }   
    }

最后给出Model类`DbDao.java`：

    import java.sql.*;

    public class DbDao {
        private Connection conn;
        private String driver;
        private String url;
        private String username;
        private String pass;

        public DbDao() {}
        public DbDao(String driver, String url, String username, String pass) {
            this.driver = driver;
            this.url = url;
            this.username = username;
            this.pass = pass;
        }
        //各属性的setter和getter方法
        public void setDriver(String driver) {
            this.driver = driver;
        }
        public void setUrl(String url) {
            this.url = url;
        }
        public void setUsername(String username) {
            this.username=username;
        }
        public void setPass(String pass) {
            this.pass = pass;
        }
        public String getDriver() {
            return this.driver;
        }
        public String getUrl() {
            return this.url;
        }
        public String getUsername() {
            return this.username;
        }
        public String getPass() {
            return this.pass;
        }

        //获取数据库连接
        public Connection getConnection() throws Exception{
            if (conn == null) {
                Class.forName(this.driver);
                conn = DriverManager.getConnection(url, username, this.pass);
            }
            return conn;
        }
        // 插入记录
        public boolean insert(String sql, Object... args) throws Exception {
            PreparedStatement pstmt = getConnection().prepareStatement(sql);
            for (int i=0; i<args.length; i++) {
                pstmt.setObject(i+1, args[i]);
            }
            if (pstmt.executeUpdate() != 1) {
                return false;
            }
            return true;
        }
        // 执行查询
        public ResultSet query(String sql, Object... args) throws Exception {
            PreparedStatement pstmt = getConnection().prepareStatement(sql);
            for (int i = 0; i < args.length; i++) {
                pstmt.setObject(i+1, args[i]);
            }
            return pstmt.executeQuery();
        }
        //执行修改
        public void moify (String sql, Object... args) throws Exception {
            PreparedStatement pstmt = getConnection().prepareStatement(sql);
            for (int i = 0; i < args.length; i++) {
                pstmt.setObject(i+1, args[i]);
            }
            pstmt.executeUpdate();
            pstmt.close();
        }
        //关闭数据库连接的方法
        public void closeConn() throws Exception {
            if (conn != null && !conn.isClosed()) {
                conn.close();
            }
        }
    }

至此，一个完整的MVC模式的web应用就完成了，其中JSP充当View，用来提供用户交流界面; Servlet充当控制器, 起调动作用；JavaBean充当模型，处理业务逻辑和数据逻辑。 

## Servlet类相关

### Filter

Filter可以看作Servlet的加强版，它是一个处理链，典型的使用流程是：使用Filter对用户请求进行预处理，然后将请求交给Servlet进行处理，最后对服务器响应进行后处理。Filter的作用主要有以下几个方面：

* 拦截HttpServletRequest进行预处理，可以修改请求数据，然后交给Servlet处理
* 拦截HttpServletResponse进行后处理，可以修改Servlet的处理结果，然后作为服务器的响应结果。

Filter的使用和Servlet非常类似，包括定义Filter类和配置Filter两步。其中Filter类必须实现`javax.servlet.Filter`接口，主要功能在`doFilter()`函数内实现。Filter的配置也可使用@webFilter Annotation进行配置。

### Listener

Listener完成对web应用内事件的监听和处理，从而允许当web应用内有事件发生时，可以回调事件监听器内的方法。Listener的使用与Servlet类似，分为定义Listener实现类和配置Listener两步。自定义的Listener类根据不同的web应用时间需实现不同的接口，常见的web监听器接口有以下几个：

* ServletContextListener: 用于监听web应用的启动和关闭
* ServletContextAttributerListener: 用于监听ServletContext范围（application）内属性的更改
* ServletRequestListener： 用于监听用户请求
* ServletRequestAttributerListener: 用于监听ServletRequest范围（request）内属性的更改
* ServletSessionListener： 用于监听用户Session的开始和结束
* ServletSessionAttributerListener: 用于监听ServletSession范围（session）内属性的更改

## 参考
[轻量级Java EE企业应用实战](http://book.douban.com/subject/6002664/)

