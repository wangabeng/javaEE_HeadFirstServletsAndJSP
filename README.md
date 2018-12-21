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
<%@ import="foo.*, java.util.*" %>
```
3 jsp表达式 不需要显式地打印 表达式以<%= 开始
```
<%= Counter.getCount() %>
```
