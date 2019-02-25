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

# 关于 request.getHeader("referer")的作用
```
在开发web程序的时候，有时我们需要得到用户是从什么页面连过来的，这就用到了referer。
它是http协议，所以任何能开发web程序的语言都可以实现,比如jsp中是：
request.getHeader("referer");
php是$_SERVER['HTTP_REFERER']。其他的我就不举例了（其实是不会其他的语言）。

js的话就是这样做：javascript:document.referrer
那它能干什么用呢？我举两个例子：
1，防止盗连，比如我是个下载软件的网站，在下载页面我先用referer来判断上一页面是不是自己网站，如果不是，说明有人盗连了你的下载地址。
2，电子商务网站的安全，我在提交信用卡等重要信息的页面用referer来判断上一页是不是自己的网站，如果不是，可能是黑客用自己写的一个表单，来提交，为了能跳过你上一页里的javascript的验证等目的。
使用referer的注意事项：
如果我是直接在浏览器里输入有referer的页面，返回是null（jsp），也就是说referer只有从别的页面点击连接来到这页的才会有内容。
我做了个实验，比如我的referer代码在a.jsp中，它的上一页面是b.htm，c.htm是一个带有iframe的页面，它把a.jsp嵌在iframe里了。我在浏览器里输入b.htm的地址，然后点击连接去c.htm，那显示的结果是b.htm，如果我在浏览器里直接输入的是c.htm那显示的是c.htm
```
参考：https://blog.csdn.net/wclxyn/article/details/7288745  
一个用过滤器防盗链的例子  
https://www.jb51.net/article/108559.htm

# servlet监听器(监听在线人数等信息)
https://blog.csdn.net/sinat_29384657/article/details/74543175

# eclipse导入jar包:
https://blog.csdn.net/czbqoo01/article/details/72803450

----------------------------------------------
# 工具包位置
/jdk/src.zip

# 安装jdbc
1 下载mysql-connector-java-5.1.47.zip  
https://dev.mysql.com/downloads/file/?id=480091   
2 解压找到 mysql-connector-java-5.1.47-bin.jar  
3 建的如果是web工程，当Class.forName("com.mysql.jdbc.Driver")时，Eclipse是不会去查找字符串，不会去查找驱动。所以需要把mysql-connector-java-5.1.10-bin.jar拷贝到tomcat下lib目录下.  
4 在Eclipse中配置
project-右键-properties-java build path-libraries-add external jars - 找到mysql-connector-java-5.1.10-bin.jar- apply即可。

# jdbc报错解决  
jsp中必须用try catch包裹  
参考demo  
http://www.runoob.com/w3cnote/jdbc-use-guide.html

# 一个完整JDBC小demo
参考http://www.runoob.com/w3cnote/jdbc-use-guide.html
```
package testjdbc;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;

public class Test {

    public static final String URL = "jdbc:mysql://localhost:3306/test";
    public static final String USER = "root";
    public static final String PASSWORD = "";

    public static void main(String[] args) throws Exception {
        //1.加载驱动程序
        Class.forName("com.mysql.jdbc.Driver");
        //2. 获得数据库连接
        Connection conn = DriverManager.getConnection(URL, USER, PASSWORD);
        //3.操作数据库，实现增删改查
        Statement stmt = conn.createStatement();
        ResultSet rs = stmt.executeQuery("SELECT * FROM table1");
        //如果有数据，rs.next()返回true
        while(rs.next()){
            System.out.println(rs.getString("name"));
        }
    }
}

```

# jsp中的Basepath
![avatar](/img/base-path.png)  
解析：  
```
request.getSchema()，返回的是当前连接使用的协议，一般应用返回的是http、SSL返回的是https；

request.getServerName()，返回当前页面所在的服务器的名字；

request.getServerPort()，返回当前页面所在的服务器使用的端口，80；

request.getContextPath()，返回当前页面所在的应用的名字。
```

```
<%
String path = request.getContextPath();
String basePath = request.getScheme()+"://"+request.getServerName()+":"+request.getServerPort()+path+"/";
%>
```
然后需要在head标签内添加如下代码
```
<base href="<%=basePath%>">
```
Basepath其实就是提供了一个默认的绝对路径，相当于：localhost:8080/项目名/，让我们在写路径的时候不再为路径错误导致的404烦恼。  
```
<a href = "NewFile-basepath.jsp" >
```
就相当于： 
localhost:8080/项目名/NewFile-basepath.jsp  

#  <welcome-file-list>的作用  
指定应用的首页  
里面可以指定多个文件，应用服务器会按从上到下的顺序搜索，如果找到就了进入该页面，如果都遍历完了还没找到就会返回404错误代码给浏览器。

# 复写方法时@Override的作用
代码错误提示 详见  
https://www.cnblogs.com/bincoding/p/5725732.html

# 过滤器使用
https://o7planning.org/en/10395/java-servlet-filter-tutorial

# 关于@WebServlet
```
编写好Servlet之后，接下来要告诉Web容器有关于这个Servlet的一些信息。在Servlet 3.0中，可以使用标注(Annotation)来告知容器哪些Servlet会提供服务以及额外信息。例如在HelloServlet.java中：

@WebServlet("/hello.view")  
public class HelloServlet extends HttpServlet { 
只要在Servlet上设置@WebServlet标注，容器就会自动读取当中的信息。上面的@WebServlet告诉容器，如果请求的URL是"/hello.view"，则由HelloServlet的实例提供服务。可以使用@WebServlet提供更多信息。

@WebServlet(  
    name="Hello",   
    urlPatterns={"/hello.view"},   
    loadOnStartup=1 
)  
public class HelloServlet extends HttpServlet {  
上面的@WebServlet告知容器，HelloServlet这个Servlet的名称是Hello，这是由name属性指定的，而如果客户端请求的URL是/hello.view，则由具Hello名称的Servlet来处理，这是由urlPatterns属性来指定的。在Java EE相关应用程序中使用标注时，可以记得的是，没有设置的属性通常会有默认值。例如，若没有设置@WebServlet的name属性，默认值会是Servlet的类完整名称。

当应用程序启动后，事实上并没有创建所有的Servlet实例。容器会在首次请求需要某个Servlet服务时，才将对应的Servlet类实例化、进行初始化操作，然后再处理请求。这意味着第一次请求该Servlet的客户端，必须等待Servlet类实例化、进行初始动作所必须花费的时间，才真正得到请求的处理。

如果希望应用程序启动时，就先将Servlet类载入、实例化并做好初始化动作，则可以使用loadOnStartup设置。设置大于0的值(默认值为-1)，表示启动应用程序后就要初始化Servlet(而不是实例化几个Servlet)。数字代表了Servlet的初始顺序，容器必须保证有较小数字的Servlet先初始化，在使用标注的情况下，如果有多个Servlet在设置loadOnStartup时使用了相同的数字，则容器实现厂商可以自行决定要如何载入哪个Servlet。
```



# jsp9大内置对象
### 1 request
```
<%  
session.setAttribute("a",  "ddddddd"); 
%>
// 通过request来获取
<%=request.getSession().getAttribute("a") %>
```
### 2 response
```
response.sendRedirect("xxx.jsp")：页面跳转。注意，之前的forward是转发，这里是跳转，注意区分。

response.setCharacterEncoding("gbk")：设置响应编码
```

### 3 session
```
<%  
session.setAttribute("a",  "ddddddd"); 
%>  
<%=session.getAttribute("ad") %>
```
### 4 application
web.xml
```
    <context-param>
        <param-name>foo</param-name>
        <param-value>bar</param-value>
    </context-param>
```

```
// 获取web.xml中foo的值
<%=application.getInitParameter("foo") %>
```

### 5 pageContext
```
>forward(String relativeUrlPath):将当前页面转发到另外一个页面或者Servlet组建上;
>getRequest():返回当前页面的request对象;
>getResponse():返回当前页面的response对象;
>getServetConfig():返回当前页面的servletConfig对象;
>getServletContext():返回当前页面的ServletContext对象,这个对象是所有的页面共享的.
>getSession():返回当前页面的session对象;
>findAttribute():按照页面,请求,会话,以及应用程序范围的属性实现对某个属性的搜索;
>setAttribute():设置默认页面范围或特定对象范围之中的对象.
>removeAttribute():删除默认页面对象或特定对象范围之中的已命名对象.
<%@ page contentType="text/html" pageEncoding="GBK"%>
<html>
<head><title>www.mldnjava.cn，MLDN高端Java培训</title></head>
<body>
<%
    // 直接从pageContext对象中取得了request
    String info = pageContext.getRequest().getParameter("info") ;
%>
<h3>info = <%=info%></h3>
<h3>realpath = <%=pageContext.getServletContext().getRealPath("/")%></h3>
</body>
</html>


<%@ page contentType="text/html" pageEncoding="GBK"%>
<html>
<head><title>www.mldnjava.cn，MLDN高端Java培训</title></head>
<body>
<%
    pageContext.forward("pagecontext_forward_demo02.jsp?info=MLDN") ;
%>
</body>
</html>
```

### 6 out
用于输出

### 7 page
### 8 config 基本用不到
### 9 exception 基本用不到

# jsp三大指令
### 1 page指令
定义页面特定的属性，如字符编码，页面响应内容类型等
```
<%@ page import="foo.*" session="false" %>
```

### 2 include指令
```
<%@ include file="XXX.html"  %>
```
### 3 taglib指令(略)

# JSP(include指令与<jsp:include>动作的区别)
```
<%@ page language= "java" contentType="text/html;charset=UTF-8" %>
<html>
    <head>
        <meta charset="utf-8">
        <title>JSPinclude动作实例</title>
    </head>
    <body>
        
        <%@ include file = "Static.txt" %>
        
        <jsp:include page="Dyamic.jsp" flush="true"></jsp:include>
    </body>
</html>

Static.txt————————————————————————————————————————

<%@ page language= "java" contentType="text/html;charset=UTF-8" %>
<form action="JSPIncludeActiveDemo.jsp" method=post>
    用户名：    <input type=text name=name><br>
    密码：    <input type=password name=password><br>
    <input type=submit value=登录>
</form>

Dyamic.jsp————————————————————————————————————————

<%@ page language= "java" contentType="text/html;charset=UTF-8" %>
<br>
用户名：<%=request.getParameter("name") %>
<br>
密码：<%=request.getParameter("password") %>
<br>

include指令与<jsp:include>动作的区别：

include指令通过file属性来指定被包含的页面。<jsp:include>动作通过page属性来指定被包含的页面。
使用include指令，被包含的文件被原封不动的插入到包含页面中使用该指令的位置，然后JSP编译器再对这个合成的文件进行编译，所以在一个JSP页面中使用include指令来包含另一个JSP页面，最终编译后的文件只有一个。（静态包含）
          使用<jsp:include>动作包含文件时，当该动作标识执行后，JSP程序会将请求转发到（注意不是重定向）被包含页面，并将执行结果输出到浏览器中，然后返回页面继续执 行后面的代码，以为web容器执行的两个文件，所以JSP编译器会分别对这两个文件进行编译。（动态包含）

          注意：（使用<jsp:include>动作通常是包含那些经常改动的文件，因为被包含的文件改动不会影响到包含文件，因此不需要对包含文件进行重新编译）
```


-----------------------------------------------------

# jsp servlet实现验证码
https://blog.csdn.net/lulei9876/article/details/8365500/#comments
https://blog.csdn.net/chenfengbao/article/details/62424279

# servlet过滤器登录验证及字符编码过滤
https://www.cnblogs.com/oumyye/p/4273330.html

# vue代替jsp做渲染的结果
jsp
```
<%@ page language="java" contentType="text/html; charset=utf-8"
    pageEncoding="utf-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Insert title here</title>
<script type="text/javascript"
    src="https://cdn.staticfile.org/vue/2.2.2/vue.min.js"></script>
    
</head>

<body>
<div id="app">
    <%
        session.setAttribute("ad", "/testWeb/images/test.png"); //把b放到session里，命名为a，  
        // String M = session.getAttribute(“a”).toString(); //从session里把a拿出来，并赋值给M
    %>
    <%=session.getAttribute("ad")%>
    <img v-bind:src="scr">
    
    <!-- 设置vue数据 -->
    <p>{{ message }}</p>
</div>  
</body>
<script>
    new Vue({
        el : '#app',
        data : {
            message : 'Hello Vue.js!',
            scr: "<%=session.getAttribute("ad")%>"
        }
    })
</script>
</html>
```
在客户端打开页面的代码.(vue是js，只有在客户端才起作用,服务器端vue的代码并没有渲染出来，从返回的代码可以看出，代码中还保留有未被渲染的{{ message }}。Vue最好用在前后端分离的项目中)
```
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Insert title here</title>
<script type="text/javascript"
    src="https://cdn.staticfile.org/vue/2.2.2/vue.min.js"></script>
    
</head>

<body>
<div id="app">
    
    /testWeb/images/test.png
    <img v-bind:src="scr">
    
    <!-- 设置vue数据 -->
    <p>{{ message }}</p>
</div>  
</body>
<script>
    new Vue({
        el : '#app',
        data : {
            message : 'Hello Vue.js!',
            scr: "/testWeb/images/test.png"
        }
    })
</script>
</html>
```

# Jsp和Servlet之间值传递的N种方法
https://zhuanlan.zhihu.com/p/31019275

<<<<<<< HEAD
# sql语句中用问号代替参数
=======
# jstl语法
一 导入jstl
1 下载 JSTL的2个库
下载地址：  
http://mvnrepository.com/artifact/org.glassfish.web/javax.servlet.jsp.jstl  
http://mvnrepository.com/artifact/javax.servlet.jsp.jstl/javax.servlet.jsp.jstl-api  
拷贝这两个文件到/ WEB-INF/lib

2 在项目上右击 - build path - confiture build path - libraries - add jars - 找到/ WEB-INF/lib刚拷贝的两个jar文件 然后选中apply
3 在项目的根目录 / webapp libraris就可以看到刚才导入的jar包了

二 使用jstl语法  
1 引用
```
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %> 
```
2 使用(获取索引值.index)
```
<c:forEach var="i" begin="1" end="5">
   Item <c:out value="${i}"/><p>
</c:forEach>
```

# java文件上传案例
https://www.cnblogs.com/xdp-gacl/p/4200090.html  
https://blog.csdn.net/javae100/article/details/79938923

# servlet实现验证码
https://www.jianshu.com/p/bc6403531661
>>>>>>> tmp
