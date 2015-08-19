教程
====

## 使用和启动H2管理系统

H2 管理系统让你能够通过一个浏览器对 H2 的 SQL 数据库进行管理操作。H2 管理系统不仅可以连接 H2 数据库，也可以连接其他支持 JDBC API 的数据库。

![](../images/console-2.png)

这是一个 C/S 应用，在服务器和客户端（浏览器）上都要运行 H2 的管理程序。根据平台不同，H2 管理系统支持多种启动应用的方式：

操作系统 | 启动
----- | -----
Windows	| 点击 [Start], [All Programs], [H2], 和 [H2 Console (Command Line)]。系统的系统托盘可以看到 H2 的图标。如果看不到可能是 Java 没有安装正确。浏览器访问<http://localhost:8082>。
Windows | 打开文件浏览器，切换目录到h2/bin，双击运行h2.bat。一个控制台窗口将弹出，如果有问题，将有错误信息在这个窗口里显示。一个浏览器窗口将被打开，指向的URL是<http://localhost:8082>，是H2管理系统的登录页面。
Any	| 双击h2*.jar文件。前提是 .jar 文件能正确的被 Java 打开。
Any	| 开启控制台窗口，切换目录到h2/bin，执行命令：`java -cp h2*.jar org.h2.tools.Server`

### 防火墙

在你启动服务时，如果你安装了防护墙，你可能会收到一个防护墙的安全警告。如果不需要其他计算机访问你这台计算机上的H2数据库，你可以让防火墙阻塞H2对外服务的端口，但是本地计算机仍可以访问这些端口。当你需要其他计算机也能访问这台计算机的H2数据库时，你需要让防火墙开放H2对外服务的端口。

有报告显示使用 Kaspersky(卡巴斯基)7.0 的防火墙时，使用 IP 地址访问本地的 H2 时，速度非常的缓慢，替代的方案是使用'localhost'代替IP 地址来访问。

一个简单的防火墙已经集成到H2的服务器中，其他的计算机缺省状态下不能连接到服务器，如果需要其他计算机能连接到H2服务器，到'Preferences'（偏好），选择'Allow connections from other computers'（允许从其他计算连接）即可

### JAVA测试

打开一个命令行窗口，输入下面的命令，检测JAVA的版本：

	java -version

如果你得到错误的信息，你可能未安装 JDK，或是需要将 Java 的可执行文件路径加入到环境变量 PATH 中。

### 错误信息'Port may be in use'(端口被占用)

你可能在启动一个 H2 控制台实例时，出现错误信息"The Web server could not be started. Possible cause: another server is already running...".（WEB服务器不能启动，可能的原因：另外一个服务器已经在运行了）。使用不同的端口，可以在一台计算机上启动多个控制台程序 ，但是一般都不会做。



## Special H2 Console Syntax
## Settings of the H2 Console
## Connecting to a Database using JDBC
## Creating New Databases
## Using the Server
## Using Hibernate
## Using TopLink and Glassfish
## Using EclipseLink
## Using Apache ActiveMQ
## Using H2 within NetBeans
## Using H2 with jOOQ
Using Databases in Web Applications
Android
CSV (Comma Separated Values) Support
Upgrade, Backup, and Restore
Command Line Tools
The Shell Tool
Using OpenOffice Base
Java Web Start / JNLP
Using a Connection Pool
Fulltext Search
User-Defined Variables
Date and Time
Using Spring
OSGi
Java Management Extension (JMX)