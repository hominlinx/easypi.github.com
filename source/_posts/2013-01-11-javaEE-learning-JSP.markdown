---
layout: post
title: "JavaEE学习之JSP技术"
date: 2013-01-11 17:35
comments: true
categories: [javaEE, note]
---


JSP(Java Server Page)与Servlet是JavaEE的基本成员，基本形式为在HTMl文件中添加Java代码，辅以使用JSP标签，在静态的HTML的基础上有了动态提供页面内容的能力，使从事java开发的人员可以将java知识运用到动态网页开发的应用中。

本文是JavaEE学习笔记的第一篇，以一个简单的使用JSP的web应用来开始JaveEE技术的学习，并且说明JSP与Servlet类之间的联系和工作原理，最后还总结了一些包括脚本语法、编译和动作指令、内置Servlet对象、和自定义标签等一些具体的JSP的技术。


<!-- more -->

## Hello JSP！

首先看一个简单的基于JSP的动态网页应用，在Eclipse中新建一个Dynamic Web Project，在WebContent文件夹下新建一JSP文件：Hello.jsp

    <%@ page language="java" contentType="text/html; charset=UTF-8"
        pageEncoding="UTF-8"%>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title> 欢迎</title>
    </head>
    <body>
    欢迎学习javaEE知识，现在时间是<br/>
    <%out.println(new java.util.Date()); %> <br/>
    </body>
    </html>

其中第一行用`<%@ %>`包起来的内容为JSP的编译指令，用来定义说明JSP文件的一些基本信息。该行之下就是一个普通的HTML文件，只是其中有一行用`<% %>`包起来，这表明其中的内容为java代码，该行用来输出当前的时间。将该工程部署到tomcat服务器上访问该JSP文件能够得到期望输出。由此可知，JSP技术可以概括为两者的结合：一为HTML技术，用来实现网页静态内容的显示; 二为java技术，用来实现动态内容的处理。JSP技术通过将两者结合在一起实现了动态网页的设计。在表面上，作为表现层技术，按照其语法约定编写JSP程序即可实现设计的需要; 本质上，JSP依靠Servlet实现。

## JSP 的实现原理

JSP的本质是Servlet，当用户向指定Servlet发送请求时，Servlet利用动态输出流动态生成HTML页面，包括每一个静态的HTML标签和所有在HTML页面中出现的内容。

为了表明JSP的运行原理，来看一个复杂一点的例子：

    <%@ page language="java" contentType="text/html; charset=UTF-8"
        pageEncoding="UTF-8"%>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title> 欢迎</title>
    </head>
    <body>
    欢迎学习javaEE知识，现在时间是<br/>
    <%out.println(new java.util.Date()); %>
    <br/>
    <!-- 在JSP中加入java代码，实现动态 -->
    <%
    for (int i = 0; i < 5; i++) {
        out.println("<font size='"+i+"'>");
    %>
    测试字体大小 </font> <br/>
    <% }%>
    </body>
    </html>

在刚才的hello.jsp文件中加入了一个for循环结构来控制输出字体的大小。为了更加清楚的了解JSP的实现原理，需要分析该JSP文件编译而成的Servlet文件，该文件可以在tomcat的work文件夹中找到（如果使用eclipse并将tomcat的项目部署在workspace的.metadata文件夹下，则work不在tomcat目录下，而在.metadata文件夹下）。找到的对应的Hello_jsp.java文件如下

    package org.apache.jsp;

    import javax.servlet.*;
    import javax.servlet.http.*;
    import javax.servlet.jsp.*;

    public final class Hello_jsp extends org.apache.jasper.runtime.HttpJspBase
        implements org.apache.jasper.runtime.JspSourceDependent {

      private static final javax.servlet.jsp.JspFactory _jspxFactory =
              javax.servlet.jsp.JspFactory.getDefaultFactory();

      private static java.util.Map<java.lang.String,java.lang.Long> _jspx_dependants;

      private javax.el.ExpressionFactory _el_expressionfactory;
      private org.apache.tomcat.InstanceManager _jsp_instancemanager;

      public java.util.Map<java.lang.String,java.lang.Long> getDependants() {
        return _jspx_dependants;
      }

      public void _jspInit() {
        _el_expressionfactory = _jspxFactory.getJspApplicationContext(getServletConfig().getServletContext()).getExpressionFactory();
        _jsp_instancemanager = org.apache.jasper.runtime.InstanceManagerFactory.getInstanceManager(getServletConfig());
      }

      public void _jspDestroy() {
      }

      public void _jspService(final javax.servlet.http.HttpServletRequest request, final javax.servlet.http.HttpServletResponse response)
            throws java.io.IOException, javax.servlet.ServletException {

        final javax.servlet.jsp.PageContext pageContext;
        javax.servlet.http.HttpSession session = null;
        final javax.servlet.ServletContext application;
        final javax.servlet.ServletConfig config;
        javax.servlet.jsp.JspWriter out = null;
        final java.lang.Object page = this;
        javax.servlet.jsp.JspWriter _jspx_out = null;
        javax.servlet.jsp.PageContext _jspx_page_context = null;


        try {
          response.setContentType("text/html; charset=UTF-8");
          pageContext = _jspxFactory.getPageContext(this, request, response,
                    null, true, 8192, true);
          _jspx_page_context = pageContext;
          application = pageContext.getServletContext();
          config = pageContext.getServletConfig();
          session = pageContext.getSession();
          out = pageContext.getOut();
          _jspx_out = out;

          out.write("\n");
          out.write("<!DOCTYPE html PUBLIC \"-//W3C//DTD HTML 4.01 Transitional//EN\" \"http://www.w3.org/TR/html4/loose.dtd\">\n");
          out.write("<html>\n");
          out.write("<head>\n");
          out.write("<meta http-equiv=\"Content-Type\" content=\"text/html; charset=UTF-8\">\n");
          out.write("<title> 欢迎</title>\n");
          out.write("</head>\n");
          out.write("<body>\n");
          out.write("欢迎学习javaEE知识，现在时间是<br/>\n");
    out.println(new java.util.Date()); 
          out.write("\n");
          out.write("<br/>\n");
          out.write("\n");
          out.write("<!-- 在JSP中加入java代码，实现动态 -->\n");

    for (int i = 0; i < 5; i++) {
        out.println("<font size='"+i+"'>");

          out.write("\n");
          out.write("测试字体大小 </font> <br/>\n");
     }
          out.write("\n");
          out.write("\n");
          out.write("\n");
          out.write("</body>\n");
          out.write("</html>");
        } catch (java.lang.Throwable t) {
          if (!(t instanceof javax.servlet.jsp.SkipPageException)){
            out = _jspx_out;
            if (out != null && out.getBufferSize() != 0)
              try { out.clearBuffer(); } catch (java.io.IOException e) {}
            if (_jspx_page_context != null) _jspx_page_context.handlePageException(t);
            else throw new ServletException(t);
          }
        } finally {
          _jspxFactory.releasePageContext(_jspx_page_context);
        }
      }
    }

由此java代码可以看出，JSP代码被编译转化成了一个继承自HttpJspBase的类，该类有三个方法`_jspInit(), _jspDestroy(), _jspService()`,分别实现初始化JSP/Servlet、销毁JSP/Servlet、和响应用户请求。在`_jspService()`中可以找到对应于上述JSP代码的实现：对于静态的HTML代码，调用`out.write()`方法直接输出; 对于java代码，则直接以原始形式存在与该方法内，用来动态实现要求。

通过该例可知，JSP被编译成了一个Servlet的类来实现设计要求，本质上还是Servlet，实际上还是运行了Servlet类的方法，因此编写JSP或者直接编写Servlet代码本质上是一样的。但是由于直接编写Servlet代码比较麻烦，开发实现慢，相比而言开发JSP代码则方便很多,能够大大提高动态网站的开发效率。需要注意的是，JSP代码向Servlet代码转化过程中由于存在编译转化的过程，因此每个JSP页面的第一次访问会比较慢。

## JSP的具体技术

### JSP注释语法
JSP的注释语法为：

    <%-- JSP注释内容 --%> 

### JSP声明
在JSP文件中的声明内容将会被转换为相应Servlet的成员变量和成员方法，符合java语法。JSP的声明语法为

    <%! 声明内容 %>

### JSP输出表达式
使用JSP的输出表达式可以代替`out.println()`语句，注意*输出表达式语法内不能有分号*。其语法为：

    <%= 输出内容 %>

### JSP脚本
使用JSP脚本语法可以方便的将java代码嵌入到HTML中，实现动态功能。其语法为：

    <% 脚本内容 %>

### JSP编译指令
JSP编译指令是通知JSP引擎的信息，不直接产生输出且都有默认值，因此无需为每个指令赋值。其语法为：

    <%@ 指令名 属性名="属性值"%>

常见的编译指令有`page，include，taglib`三个：

* page: 通常位于JSP顶端，有`language, extends, import, session, buffer, autoFlush, info, errorPage, isErrorPage, contentType, pageEncoding`等属性，用来说明语言、继承父类、导入包等。
* include: 实现静态导入外部文件的功能，能将外部文件嵌入到该文件并且解析其JSP语句。该导入会将目标页面的其他编译指令导入，如果与本页编译指令冲突则会报错。还可以通过JSP的动作指令`<jsp:include>`实现动态导入，不会导入目标页面的其他编译指令。
* taglib: 用于访问和定义自己的标签库。

### JSP动作指令
JSP动作指令是运行时的动作，是JSP脚本的标准化写法。其语法为：

    <指令名  属性名="属性值" />

常用的动作指令有：

* jsp:forward: 用于将页面响应转发到另外的界面
* jsp:param: 用于传递参数，需与其他支持参数的标签一起使用
* jsp:include: 用于动态引入另一个JSP页面
* jsp:plugin: 用于下载javaBean或Applet到客户端执行
* jsp:useBean: 创建一个javaBean实例
* jsp:setProperty: 设置javaBean实例的属性
* jsp:getProperty: 获得javaBean实例的属性

### JSP脚本的内置对象
JSP脚本中有9个内置对象，是Servlet API接口的实例，可以直接使用。

* application： java.servlet.ServletContext的实例，代表JSP所属web应用本身。
* config：java.servlet.ServletConfig的实例，代表该JSP的配置信息。
* exception: java.lang.Throwable的实例，该实例代表其它页面的异常和错误，只对errorPage才可使用。
* out： javax.servlet.jsp.JspWriter的实例，代表JSP页面的输出流，用于形成HTML页面
* page: java.lang.Object的实例，代表该页面本身，就是该Servlet的this。
* pageContext: javax.servlet.jsp.PageContext的实例，代表该JSP页面上下文，可用此访问页面中的共享数据。
* request: javax.servlet.http.HttpServletRequest的实例，代表客户端的一次请求，所有请求参数都在此对象中。
* response: javax.servlet.http.HttpServletResponse的实例，代表服务器对客户端的响应，常用与重定向。
* session: javax.servlet.http.HttpSession的实例，代表一次会话。

有上述的java代码可以看出，其中request和response是`_jspService()`的形参，而其他对象都在该方法内创建，为该方法的局部变量，因此在JSP脚本中可直接使用，不需再次声明。`_jspService()`方法用来完成解析客户端请求数据、处理客户请求、和生成响应数据的工作。但是由于JSP和Servlet等通常有web服务器进行调用，二者之间并不互相调用，为了实现二者之间的数据交换，web服务器定义了四个类似map的结构充当中介的角色，实现二者之间的数据交换。这四个结构根据范围由大到小依次是`application > session > request > page`：

* application: 对整个web应用有效，其中的数据可被该应用下的JSP和Servlet访问。
* session: 对一次会话有效，其中的数据可被该次会话中是JSP和Servlet访问
* request: 对一次请求有效，其中的数据可被该次请求中是JSP和Servlet访问
* page: 只对当前页面有效，其中的数据可以被该JSP页面的脚本，声明中访问。

对于客户端发送的请求，其请求参数有GET和POST两种方式。二者之间存在一些差异：

* GET: 两种情况下使用该类方式请求，一是直接在浏览器地址栏输入访问地址时，二是提交表单时form元素的method属性为设置或设为get时。该类请求参数的名和值会被转换为字符串附加于地址之后，用户可以看到，且传送数据量较小。
* POST: 提交表单的form元素的method属性设为post时使用该类方式请求。在地址栏看不到请求参数值，传送数量较大。    

对于请求的传递，会用到转发（forward）和重定向（redirect）两种方式。二者的差异为：

* 执行forward仍然为该次请求，而redirect则生成第二次请求。
* 由于是同一次请求，forward的请求参数保留且不变; redirect则不同，生成的新的请求不会保留上次请求的参数。
* forward处理浏览器的地址栏不会变化，redirect则会改为重定向的目标URL，与在地址栏输入新URL回车效果相同。

### JSP的自定义标签
通过使用JSP的自定义标签库规范，可以扩展JSP的标签，将复杂的功能封装在简单的标签内，提高开发效率。使用该技术主要分三步：

* 开发自定义标签处理类: 该类继承自javax.servlet.jsp.tagext.SimpleTagSupport, 通过重写doTag()方法实现功能。
* 建立TLD（Tag Library Definition）文件实现标签名与标签处理类的关联。
* 在JSP文件中，通过编译指令指明使用的标签库，即可在JSP中使用自定义的标签。

## 参考
[轻量级Java EE企业应用实战](http://book.douban.com/subject/6002664/)