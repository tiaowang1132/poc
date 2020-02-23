# FASTJSON RCE 1
Fastjson反序列化漏洞复现
## 1、       介绍：
## 2、       环境搭建：
### 1、       下载问题版本fastjson的war包（war包定义），地址：`https://raw.githubusercontent.com/yaofeifly/vulhub/master/fastjson/vuln/fastjson-1.0.war`
### 2、       安装tomcat
### 3、       使用tomcat部署war包：
先将下载的war文件复制到tomcat的解压路径中的webapps下<br>
![image](https://github.com/tiaowang1132/poc/blob/master/vuln-test-image/f.1.png)<br>
再编辑tomcat解压路径下的conf/server.xml<br>
![image](https://github.com/tiaowang1132/poc/blob/master/vuln-test-image/f.2.png)<br>
在host标签中添加
```<Context path="/" docBase="fastjson.war" debug="0" privileged="true" reloadable="true"/>；```其中docBase内容为对应的war包名<br>
![image](https://github.com/tiaowang1132/poc/blob/master/vuln-test-image/f.3.png)<br>
修改好之后，进入tomcat解压路径中的bin路径，找到startup.bat，点击运行。打开浏览器访问`127.0.0.1:8080/fastjson/`，默认端口8080，可以通过修改sever.xml更改端口。<br>
![image](https://github.com/tiaowang1132/poc/blob/master/vuln-test-image/f.4.png)<br>
环境搭建完毕。

## 3、       漏洞复现：
准备payload:
先使用burpsuite抓包，并将get改为post，并添加如下post表单：<br>
![image](https://github.com/tiaowang1132/poc/blob/master/vuln-test-image/f.5.png)<br>
Poc.java:<br>
![image](https://github.com/tiaowang1132/poc/blob/master/vuln-test-image/f.6.png)<br>
`Runtime.getRuntime().exec()`中填写想要执行的命令。上图执行curl命令
编译该java文件为class文件：
命令：javac  Poc.java
使用base64将生成的class文件编码：<br>
![image](https://github.com/tiaowang1132/poc/blob/master/vuln-test-image/f.7.png)<br>
输出的字段即为payload中的_bytecodes。
构造payload如下：<br>
![image](https://github.com/tiaowang1132/poc/blob/master/vuln-test-image/f.8.png)<br>
发送post包至目标url：<br>
![image](https://github.com/tiaowang1132/poc/blob/master/vuln-test-image/f.9.png)<br>
查看我的服务器：多了一条记录<br>
![image](https://github.com/tiaowang1132/poc/blob/master/vuln-test-image/f.10.png)<br>
证明命令执行成功。
