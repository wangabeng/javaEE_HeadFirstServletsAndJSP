# 几种不同的jsp语法
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
182
