入门
====

## 内嵌 H2 在应用中

H2 可以使用内嵌模式或者是服务器模式。使用内嵌模式，需要做如下步骤：

* 添加`h2*.jar` 到 classpath (H2 没有任何依赖)
* 使用 JDBC 驱动类: `org.h2.Driver`
* 数据库的 URL 是`jdbc:h2:~/test`,在你本地目录打开数据库`test`
* 此时，新的数据库就自动创建了

## 使用 H2 控制台

控制台运行你在浏览器访问 SQL 数据库。

![Web Browser - H2 Console Server - H2 Database ](../images/console-2.png)

如果没有正常运行，参见[教程](../Tutorial/README.md))一章。

### 步骤 

#### 安装

使用 Windows Installer 安装软件

#### 启动控制台

点击 [Start], [All Programs], [H2], and [H2 Console (Command Line)]:

![Screenshot: start H2 Console](../images/quickstart-1.png)

出现控制台窗口:

![Screenshot: H2 running](../images/quickstart-2.png)

同时浏览器会弹出一个新页面<http://localhost:8082>。可能会有防火墙的安全警告。如果是在本地访问数据库，就不用处理。

#### 登陆

选择 [Generic H2] ，点击 [Connect]:

![Screenshot: login](../images/quickstart-3.png)

现在可以登陆了

#### 示例

点击 [Sample SQL Script]:

![Screenshot: click on the sample SQL script](../images/quickstart-4.png)

SQL 命令就出现在命令输入区

#### 执行

点击 [Run]

![Screenshot: click Run](../images/quickstart-5.png)

在左侧，一个的条目 TEST 就添加到了数据库图标下。操作和结果是显示如下脚本 

![Screenshot: click Run](../images/quickstart-6.png)

#### 断开连接

点击 [Disconnect]:

![Disconnect icon](../images/icon_disconnect.gif)

来关闭连接

#### 结束

关闭控制台窗口。详见[教程](../Tutorial/README.md))一章。