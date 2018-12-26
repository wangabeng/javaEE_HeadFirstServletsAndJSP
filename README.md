# 初始化一个servlet项目(chp1)
1 项目所在目录 E:\apache-tomcat7\webapps  

2 项目根目录  
  ch1/WEB-INF/classes （编译的class文件夹） 
  ch1/WEB-INF/web.xml （xml配置文件）
  ch1/WEB-INF/src （java文件 工作临时目录 临时） 
  ch1/WEB-INF/servlet-api.jar (tomcat提供的工具包文件 临时)  

3  ch1/WEB-INF/web.xml （xml配置文件）   
```
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://java.sun.com/xml/ns/javaee"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
                      http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
  version="3.0"
  metadata-complete="true">

  <servlet>
    <servlet-name>Chapter1 Servlet</servlet-name>
    <servlet-class>Ch1Servlet</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>Chapter1 Servlet</servlet-name>
    <url-pattern>/Serv1</url-pattern>
  </servlet-mapping>

</web-app>

```
4 ch1/WEB-INF/src/Ch1Servlet.java  
```
import javax.servlet.*;
import javax.servlet.http.*;
import java.io.*;

public class Ch1Servlet extends HttpServlet {
  public void doGet(HttpServletRequest reque, HttpServletResponse response) throws IOException {
    PrintWriter out = response.getWriter();
    java.util.Date today = new java.util.Date();
    out.println("<html>" +
        "<body>" +
        "<h1>hahah</h1>" + 
        "</body>" +
        "</html>"
      );
  }
}
```
5 编译java文件到classes中  
```
cd E:\apache-tomcat7\webapps\ch1
javac -classpath ./servlet-api.jar -d classes ./src/Ch1Servlet.java
```
6 重新启动tomcat

# servlet获取页面参数
1 页面传递参数  color 值位a
```
<select name='color'>
  <option value='a'>a</option>
  <option value='b'>b</option>
  <option value='c'>c</option>
</select>

<input type="checkbox" name="size" value="flow">
<input type="checkbox" name="size" value="flow2">
<input type="checkbox" name="size" value="flow3">
```
2 servlet获取参数  
```
String color = request.getParameter("color");// 字符串
String[] sizes = request.getParameterValues("size"); // 获取字符串数组
```
3 获取请求头
request.getHeader("User-Agent")
与cookie有关的  
request.getCookies()
与session有关的  
request.getSession()
与请求的hhtp方法有关的    
request.getMethod()
输入的流  
request.getInputStream()

# 重定向和需求转发
重定向是发生在客户端
```
response.sendRedirect("http://www.baidu.com");
```
需求转发是在服务器端
```
RequestDispatcher view = request.getRequestDispatcher("result.jsp");
view.forward(request, response);
```

# chp5 属性和监听者
## 不要把email信息账号信息 硬编码到servlet类中

## servletConfig和servletContext
每个servlet有一个servletConfig  
每个web应用有一个servletContext

# 如果你的应用分布在多个服务器上，可能在一个集群环境中，那么web应用实际上可以有多个ServletContext。一个servletContext确实只对应一个应用，但是前提是在一个JVM中（JVM可以理解成我的window系统中安装的centos虚拟机）。如果在一个分布式的环境，这会带来问题的。

# 案例 设置servletConfig  并使用之
1 在DD配置文件中特定的servlet中配置servletConfig
```
 <servlet>
     <servlet-name>dateServlet</servlet-name>
     <servlet-class>abeng.Wangabeng</servlet-class>
     <init-param>
        <param-name>foo</param-name>
        <param-value>value</param-value>  
     </init-param>
 </servlet>
```
2 在servlet类中获取该值  
```
package abeng;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import java.util.Date;
import java.io.IOException;
import java.io.PrintWriter;
public class Wangabeng extends HttpServlet {
    public void service(HttpServletRequest request, HttpServletResponse response) throws ServletException,IOException {
         Date date=new Date();
         response.setContentType("text/html");
         PrintWriter out=response.getWriter();
         out.println("now:"+date);
         String paremeterValue = getServletConfig().getInitParameter("foo"); // 获取该值
         out.println("config值" + paremeterValue);
         out.close();
     }
}
```
# 案例 设置servletContext  并使用之
1 在dd文件中配置
```
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd" id="WebApp_ID" version="3.0">
  <display-name>firstservlet</display-name>
  <welcome-file-list>
    <welcome-file>index.html</welcome-file>
    <welcome-file>index.htm</welcome-file>
    <welcome-file>index.jsp</welcome-file>
    <welcome-file>default.html</welcome-file>
    <welcome-file>default.htm</welcome-file>
    <welcome-file>default.jsp</welcome-file>
  </welcome-file-list>
  <context-param>
    <param-name>foo</param-name>
    <param-value>bar</param-value>
  </context-param>
   <servlet>
       <servlet-name>dateServlet</servlet-name>
       <servlet-class>abeng.Wangabeng</servlet-class>
       <init-param>
          <param-name>foo</param-name>
          <param-value>value</param-value>  
       </init-param>
   </servlet>
   <servlet-mapping>
       <servlet-name>dateServlet</servlet-name>
       <url-pattern>/date</url-pattern>
   </servlet-mapping>  
</web-app>
```
2 在类中获取该servletContext值
```
 String paremeterContext = getServletContext().getInitParameter("foo");
 out.println("context值" + paremeterContext);
```

# servletConfig和servletContext遇到的问题
都是在servlet初始化的时候 才能获取到这两个值。注意，只能获取改值，不能在servlet中设置。

# 在eclipse中创建servlet或jsp项目  
1 安装exlipse for javaee （要用就版的 新版的是2018年12月份出的 不知道怎么用 我的系统安装备份中保存有）  
2 在eclipse中配置tomcat 见 https://www.cnblogs.com/conquerorren/p/7879083.html  
3 在eclipse中创建servlet或jsp项目  
（配置jsp项目 见https://www.cnblogs.com/conquerorren/p/7879083.html）  
（配置servlet项目 见https://www.cnblogs.com/mosese/p/4558776.html）  

# 用exlipse以后 免去了手工编译java的烦恼 工作效率大大提高了

# 用数据库来初始化一个参数配置 替代servletConfig 和servletContext（只能是string）  


## servletContextListerner基本职能
1 在上下文初始化时执行：
  1 从ServletContext得到上下文初始化参数  
  2 使用初始化参数查找名建立一个数据库连接  
  3 把数据库连接存储为一个属性，使得web应用的各个部分都能访问。
2 上下文撤销时得到通知  
  1 关闭数据库连接  


# 教程一个简单的servletContextListerner
此案例的完整执行流程
容器读取DD文件 -- 
容器为这个应用创建新的ServletContext -- 
容器为上下文初始化参数创建名值对 -- 
容器将名/值参数既爱哦给ServletContext --  
容器创建一个新的MyServletContextListener类 --  
容器调用监听者的contextInitialized方法 （获取参数 创建Dog 创建新的dog参数并把dog实例传给dog参数） --  
容器创建一个新的serlet（也就是说 利用初始化参数 建立一个新的ServletContext） --  
servlet得到一个请求 执行Wangabeng类的内容 --  

概括起来执行顺序  
先执行MyServletContextListener（监听者） 后执行Wangabeng（servlet类） 

见如下示意图：
![avatar](/img/监听者工作流程.jpg)
 
1 在DD部署文件中放一个 <listener> 元素
```
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd" id="WebApp_ID" version="3.0">
  <context-param>
    <param-name>bread</param-name>
    <param-value>bread value hahaah</param-value>
  </context-param>
  <listener>
    <listener-class>abeng.MyServletContextListener</listener-class>
  </listener>
   <servlet>
       <servlet-name>testlistner</servlet-name>
       <servlet-class>abeng.Wangabeng</servlet-class>
   </servlet>
   <servlet-mapping>
       <servlet-name>testlistner</servlet-name>
       <url-pattern>/testlistner</url-pattern>
   </servlet-mapping>  
</web-app>
```

2 在java/src新建的包中 新建一个类
MyServletContextListener.java
```
package abeng;

import javax.servlet.*;

public class MyServletContextListener implements ServletContextListener {
  public void contextInitialized (ServletContextEvent event) {
    // do sth
  ServletContext sc = event.getServletContext();
  String dogBreed = sc.getInitParameter("bread");
  
  Dog d = new Dog(dogBreed);
  sc.setAttribute("dog", d);
  }
  public void contextDestroyed (ServletContextEvent event) {
      // do sth
    }
}
``` 

3 在java/src新建的包中 新建一个Dog类
```
package abeng;

public class Dog {
  private String breed;
  public Dog(String breed) {
    this.breed = breed;
  }
  public String getBreed () {
    return breed;
  }
}

```

4 在java/src新建的包中 新建一个servlet类 Wangabeng.java
```
package abeng;

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.*;

public class Wangabeng extends HttpServlet {
    public void service(HttpServletRequest request, HttpServletResponse response) throws ServletException,IOException {
         
         response.setContentType("text/html");
         PrintWriter out=response.getWriter();
         
         out.println("test lisentener<br>");
         
         Dog dog = (Dog) getServletContext().getAttribute("dog");
         
         out.println("Dog's breed is:" + dog.getBreed());
     }
}
```

5 重新启动tomcat 
浏览器输入 http://localhost:8080/secondDynamicWeb/testlistner
```
test lisentener
 Dog's breed is:bread value hahaah 
```

# chep5 属性
属性就是一个对象，可能设置（绑定）到另外3个servlet API对象中的某一个，包括 ServletContext HttpServletRequest(或ServletRequest)或者HttpSession。可以把它简单地认为是一个映射实例对象中的名/值对（名是一个String 值是一个Object）

# 属性和参数区别
![avatar](/img/属性和参数区别.png)

# 属性API
![avatar](/img/属性API.png)

# 给ServletContext加锁 保证线程安全 不可取

# 对HttpSession同步来保护会话属性
![avatar](/img/同步session.png)
这样存在的问题：一个用户自身每次 同时打开A浏览器和B浏览器 也只能访问一个servlet

# 如何保证一个servlet一次只能得到并处理一个请求(page200) 无解？

# 请求属性和请求分派(page205)  
![avatar](/img/请求属性和请求分派.png)

# 得到RequestDispatcher的两种方法  
1 从ServletRequest得到  
RequestDispatcher view = request.getRequestDispatcher("result.jsp")  
2 从ServletContext获得  
RequestDispatcher view = getServletContext().getRequestDispatcher("result.jsp")  
然后执行view.forward(request, response)  


# chp6 会话状态  
# 初次设置session
```
package com;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.*; // session工具

import java.util.Date;
import java.io.IOException;
import java.io.PrintWriter;
public class Ben extends HttpServlet {
    public void service(HttpServletRequest request, HttpServletResponse response) throws ServletException,IOException {
         Date date=new Date();
         response.setContentType("text/html");
         PrintWriter out=response.getWriter();
         out.println("now:"+date);
         String paremeterValue = getServletConfig().getInitParameter("foo"); // 获取该值
         out.println("config值" + paremeterValue);
         
         HttpSession session = request.getSession(false);
         if (session == null) {
           session = request.getSession();
           out.println("isNew");
         } else {
           out.println("no new");
         }
         out.close();
     }
}

```

# Servlet中session获取
设置  
session.setAttribute("zhoulitong", "very good!"); 
获取  
session.getAttribute("zhoulitong");  

Session写入：
```
public void doGet(HttpServletRequest request, HttpServletResponse response)  
        throws ServletException, IOException {  
      
        HttpSession session = request.getSession(true);  
        session.setAttribute("ip", request.getRemoteAddr());  
        session.setAttribute("zhoulitong", "very good!");  
          
        response.getWriter().println("SetSession OK!");  
}  
```
Session读取：  
```
public void doGet(HttpServletRequest request, HttpServletResponse response)  
        throws ServletException, IOException {  
  
    HttpSession session = request.getSession(true);  
    String ip = (String)session.getAttribute("ip");  
    String zhoulitong = (String)session.getAttribute("zhoulitong");  
    response.getWriter().println("ip=" + ip +","+ zhoulitong);  
}  
```
# 删除session的三种方法
1 在DD中配置会话超时(15是15分钟)  
```
<web-app --->
  <servlet>
    ---
  </servlet>
  <session-config>
    <session-timeout>15</session-timeout>
  </session-config>  
</web-app>
```
2 设置特定会话的会话超时(20 × 60为20分钟，如果客户在20分钟内没有对此会话做任何请求，就会杀死它)  
```
session.setMaxInactiveInterval(20*60)
```
3 将session设置为无效  
```
session.inValidate(); // 将session设置为无效
String fooVlaue = (String)session.getAttribute('foo'); // 此时会抛出异常
```

# chp7 几种不同的jsp语法
1 Scriptlet <%  %>  
jsp中嵌套java
```
<%
  out.println(Counter.getCount());
%>
```

2 jsp指令 <%@ %> 以<%@开始 即代表是一个指令  
比如导入包指令(导入多个包)  
```
<%@ page import="foo.*, java.util.*" %>
```
3 jsp表达式 不需要显式地打印 表达式以<%= 开始 表达式后面没有分号  
```
<%= Counter.getCount() %>
```
4 jsp声明静态变量和方法，即servlet类的变量<%! int count = 0; %>，以<%!开头    
```
<%! int count = 0; %> <!-- count是一个全局变量 -->
<%= ++count %>
```
声明一个全局方法  
```
<%! int count = 0; %>
<%!= int doubleCount () {
    count = count * 2;
    return count;
  } 
%>
```