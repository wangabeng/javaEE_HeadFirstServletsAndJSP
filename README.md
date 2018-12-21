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
aaaa
