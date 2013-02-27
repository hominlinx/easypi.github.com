---
layout: post
title: "JavaEE学习之Hibernate技术"
date: 2013-02-27 16:05
comments: true
categories: [javaEE, note]
---

由于目前主流的数据库依然是关系数据库，而Java语言是面向对象的编程语言，二者之间发展的不协调导致二者结合使用时非常麻烦。为了解决这种问题，对象/关系数据库映射（ORM）这种规范完成了面向对象语言与关系数据库之间的映射。当使用了ORM框架以后就可以使用面向对象的方法来操作持久化对象，而ORM框架将这些操作转换为底层的SQL操作。Hibernate就是这样一种ORM工具，由于其开源免费、轻量级封装、可扩展性好等特点成为流行的ORM工具。

<!-- more -->

## Hibernate框架使用流程

### 0. 为web应用添加Hibernate支持

将下载的hibernate-v.jar和lib路径下的required,bytecode,jpa子目录下的所有jar包添加到应用的类加载路径下，即可使用hibernate。

### 1. 开发PO（持久化对象）

持久化对象可以实现以面向对象的方式进行数据库操作,hibernate中的持久化对象由普通java类和映射文件两部分构成。一个简单的POJO类如下

    public class News {
	    private Integer id;
	    private String title;
	    private String content;

	    public Integer getId() {
		    return id;
	    }
	    public void setId(Integer id) {
		    this.id = id;
	    }

	    public String getTitle() {
		    return title;
	    }
	    public void setTitle(String title) {
		    this.title = title;
	    }

	    public String getContent() {
		    return content;
	    }
	    public void setContent(String content) {
		    this.content = content;
	    }
    }

为了使这个简单的java类具有持久化操作的能力，Hinernate采用映射文件理解持久化类与数据表之间的对应关系，映射文件为xml格式，内容如下：

    <?xml version="1.0" encoding="UTF-8"?>

    <!DOCTYPE hibernate-mapping PUBLIC 
	    "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
	    "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">

    <hibernate-mapping>
	    <class name="News" table="news_table">
		    <id name="id" type="java.lang.Integer">
			    <column name="id" />
			    <generator class="identity" />
		    </id>
		    <property name="title" type="java.lang.String">
			    <column name="title" />
		    </property>
		    <property name="content" type="java.lang.String">
			    <column name="content" />
		    </property>
	    </class>
    </hibernate-mapping>

通过对hibernate-mapping中的class进行配置，即可完成持久化对象的开发。

### 2. 编写Hibernate通用配置信息

对于web应用中连接数据库，以及数据库的配置信息是通用的，通过配置Hibernate的通用信息进行指定要连接的数据库，登录用户名和密码等。该配置通过xml文件完成，内容如下：

    <?xml version="1.0" encoding="UTF-8"?>

    <!DOCTYPE hibernate-configuration PUBLIC
	    "-//Hibernate/Hibernate Configuration DTD 3.0//EN"
	    "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">
    <hibernate-configuration>
	    <session-factory>
		    <property name="hibernate.connection.driver_class">com.mysql.jdbc.Driver</property>
		    <property name="hibernate.connection.password">32147</property>
		    <property name="hibernate.connection.url">jdbc:mysql://localhost:3306/hibernate</property>
		    <property name="hibernate.connection.username">root</property>
		    <property name="hibernate.dialect">org.hibernate.dialect.MySQLInnoDBDialect</property>
	        <property name="hibernate.format_sql">true</property>
		    <property name="hibernate.hbm2ddl.auto">update</property>	
	
		    <mapping resource="org/crazyit/app/domain/News.hbm.xml"/>
					       
	    </session-factory>
    </hibernate-configuration>

### 3. 编写操作类代码，使用面向对象的方法操作数据库

在完成Hibernate的配置后，即可使用面行对象的方法来操作数据库了，在此类的方法中实现对数据库的操作有以下几步。

1. 获取第二步中的通用配置信息
2. 创建SessionFactory
3. 创建Session
4. 开始事务，用面向对象的方法进行处理，最后提交处理
5. 关闭Session

以下代码实现往数据库添加一条信息的功能。

    package hou;

    import News;
    import org.hibernate.Session;
    import org.hibernate.SessionFactory;
    import org.hibernate.Transaction;
    import org.hibernate.cfg.Configuration;

    public class NewsManager {
	
	    public static void main(String[] args)
		    throws Exception {
		    Configuration conf = new Configuration().configure();
		    SessionFactory sf = conf.buildSessionFactory();
		
		    Session sess = sf.openSession();

		    Transaction tx = sess.beginTransaction();

		    News n = new News();

		    n.setTitle("一条信息");
		    n.setContent("信息内容");

		    sess.save(n);
		    tx.commit();
		    sess.close();
		    sf.close();
	    }
    }


## 参考
[轻量级Java EE企业应用实战](http://book.douban.com/subject/6002664/)
