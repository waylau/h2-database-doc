入门
====

欢迎使用 H2。 H2 是一个 Java SQL 数据库。H2 的主要特点是:

* 运行很快,开源,支持 JDBC API
* 支持嵌入模式和服务器模式;是一个内存数据库
* 基于浏览器控制台应用程序
* 文件很小大，jar文件约 1.5 MB

## 嵌入 H2 到 应用中

支持嵌入模式和服务器模式。若使用嵌入模式，需做如下步骤：

* 添加 `h2*.jar` 到 classpath (H2 没有任何依赖)
* 使用 JDBC 驱动类 : org.h2.Driver
* 数据库的 URL是 `jdbc:h2:~/test`,在你的用户目录打开数据库`test`
* 这样新的数据库就自定创建了 

## H2 控制台程序

控制台允许你通过浏览器访问 SQL 数据库

![](../images/console-2.png)

下面演示步骤是 Windows 平台。

### 安装

使用 Windows Installer（可以在 <http://www.h2database.com/html/download.html> 下载）

### 启动控制台

点击 [Start], [All Programs], [H2], 和 [H2 Console (Command Line)]:

![](../images/quickstart-1.png)

此时就能看到控制台界面

![](../images/quickstart-2.png)

同时，一个浏览器窗口将被打开，指向的URL是<http://localhost:8082>，是 H2 管理系统的登录页面。有可能会有一个防火墙的提示信息，如果不设置防火墙，将只能在本地访问。

### 登陆

选择 [Generic H2] 并点击 [Connect]:

![](../images/quickstart-3.png)

这样你就登陆了

### 示例

点击 [Sample SQL Script]:

![](../images/quickstart-4.png)

SQL 命令出现在命令行区域

### 执行

点击 [Run]

![](../images/quickstart-5.png)

在左侧，可以看到一个新的 TEST 被添加。操作的结果显示如下脚本：

![](../images/quickstart-6.png)

### 断开

点击 [Disconnect]

![](../images/icon_disconnect.gif)

来断开连接

### 关闭

关闭控制台窗口即可。


更详细的步骤说明，可以参阅[教程](../Tutorial/README.md)一章