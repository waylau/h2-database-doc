
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

### 使用其他端口

如果端口已经被其他应用占用，你需要使用其他端口来启动 H2 控制台。改变 H2 的控制台端口需要修改配置文件`.h2.server.properties`。这个文件存储在用户目录下(在 Windows 系统中，这个文件通常在 `Documents and Settings/<username>`)。这个相应的入口实体是 webPort.

### 使用浏览器连接到服务器

服务器启动成功后，你就可以使用 WEB 浏览器访问服务，浏览器需要支持JavaScript。在启动的服务器上启动浏览器，打开URL <http://localhost:8082>。在启动服务器之外的计算机上，你需要提供启动服务器的IP地址，如<http://192.168.0.2:8082>.如果你在服务器上启用了SSL， URL需要使用https://开头.

### 多个并发会话

支持多个并发的浏览器会话。由于数据对象是存储在服务器上的，同时工作的会话数受限于服务器的内存。

### 登录

在登录页，你提交连接信息就可以登录到数据库。设置 JDBC 作为连接数据库的驱动，填入JDBC URL、用户名、密码，单击[Connect]。你能保存和恢复以前设置的信息，这些设置都存储在属性文件中。

### 错误信息

错误信息用红色标识，你能通过单击消息显示或隐藏异常的堆栈信息。

### 附加数据库驱动

通过增加 jar 的本地驱动文件到环境变量来附加数据库驱动 (MySQL, PostgreSQL, HSQLDB,...)。环境变量包括 H2DRIVERS 和 CLASSPATH，以 Windows 为例：如要增加数据库驱动`C:\Programs\hsqldb\lib\hsqldb.jar`, 设置环境变量 H2DRIVERS 为 `C:\Programs\hsqldb\lib\hsqldb.jar`

多个驱动可以被设置，每个驱动之间通过`;`分号分隔（Windows），其他系统通过`:`冒号分隔。在路径中空格被支持，但是这些设置不能被引用。

### 使用 H2 控制台
 
H2 控制台主页面分为三个主要的部分：顶部的工具栏，左边的是对象树，右边的是查询和结果输出栏。数据库对象（如表）都被列在左边的树形上。在查询栏上输入SQL语句点击 [Run]，结果就被输出到命令行的下面。

### 增加表名和字段名

可以通过在树上点击增加表名和字段名到脚本。如果单击表，当这个查询栏是空的时候，`SELECT * FROM... `将被自动增加到查询栏。当输入一个表的查询时，对象树上将自动的展开这张表。例如，你输入`SELECT * FROM TEST T WHERE T`，对象树上的表 TEST 将自动的展开

### 断开连接和停止应用

断开数据库，点击工具栏上的  [Disconnect] 即可，这个时候，数据库服务仍在继续运行，等待着一个新的会话进行连接。

停止服务需要右键点击系统托盘的 H2 图标，选择 [Exit]。如果没有系统托盘的 H2 图标，切换到 [Preferences] 单击 [Shutdown]，在Windows 上在服务器启动的窗口下按[Ctrl]+[C]，或者直接关闭 Windows 上的控制台窗口。

## H2 控制台的特殊语法

H2 控制台支持几个内置命令。这些都在 H2 控制台做解析,所以他们使用任何数据库。内置命令需要在声明的开始(在任何备注之前),否则他们是无法正确解析。如果有疑问,之前添加`;`命令。

<table class="main">
    <tbody><tr>
        <th>Command(s)</th>
        <th>Description</th>
    </tr>
    <tr>
        <td class="notranslate"> @autocommit_true;<br> @autocommit_false; </td>
        <td> Enable or disable autocommit. </td>
    </tr>
    <tr>
        <td class="notranslate"> @cancel; </td>
        <td> Cancel the currently running statement. </td>
    </tr>
    <tr>
        <td class="notranslate"> @columns null null TEST;<br> @index_info&nbsp;null&nbsp;null&nbsp;TEST;<br> @tables;<br> @tables null null TEST;<br>
        </td>
        <td> Call the corresponding <code class="notranslate">DatabaseMetaData.get</code> method. Patterns are case sensitive (usually identifiers are uppercase). For information about the parameters, see the Javadoc documentation. Missing parameters at the end of the line are set to null. The complete list of metadata commands is: <code class="notranslate"> @attributes, @best_row_identifier, @catalogs, @columns, @column_privileges, @cross_references, @exported_keys, @imported_keys, @index_info, @primary_keys, @procedures, @procedure_columns, @schemas, @super_tables, @super_types, @tables, @table_privileges, @table_types, @type_info, @udts, @version_columns </code> </td>
    </tr>
    <tr>
        <td class="notranslate"> @edit select * from test; </td>
        <td> Use an updatable result set. </td>
    </tr>
    <tr>
        <td class="notranslate"> @generated&nbsp;insert&nbsp;into&nbsp;test()&nbsp;values(); </td>
        <td> Show the result of <code class="notranslate">Statement.getGeneratedKeys()</code>. </td>
    </tr>
    <tr>
        <td class="notranslate"> @history; </td>
        <td> List the command history. </td>
    </tr>
    <tr>
        <td class="notranslate"> @info; </td>
        <td> Display the result of various <code class="notranslate">Connection</code> and <code class="notranslate">DatabaseMetaData</code> methods. </td>
    </tr>
    <tr>
        <td class="notranslate"> @list select * from test; </td>
        <td> Show the result set in list format (each column on its own line, with row numbers). </td>
    </tr>
    <tr>
        <td class="notranslate"> @loop 1000 select ?, ?/*rnd*/;<br> @loop 1000 @statement select ?; </td>
        <td> Run the statement this many times. Parameters (<code class="notranslate">?</code>) are set using a loop from 0 up to x - 1. Random values are used for each <code class="notranslate">?/*rnd*/</code>. A Statement object is used instead of a PreparedStatement if <code class="notranslate">@statement</code> is used. Result sets are read until <code class="notranslate">ResultSet.next()</code> returns <code class="notranslate">false</code>. Timing information is printed. </td>
    </tr>
    <tr>
        <td class="notranslate"> @maxrows&nbsp;20; </td>
        <td> Set the maximum number of rows to display. </td>
    </tr>
    <tr>
        <td class="notranslate"> @memory; </td>
        <td> Show the used and free memory. This will call <code class="notranslate">System.gc()</code>. </td>
    </tr>
    <tr>
        <td class="notranslate"> @meta&nbsp;select&nbsp;1; </td>
        <td> List the <code class="notranslate">ResultSetMetaData</code> after running the query. </td>
    </tr>
    <tr>
        <td class="notranslate"> @parameter_meta&nbsp;select&nbsp;?; </td>
        <td> Show the result of the <code class="notranslate">PreparedStatement.getParameterMetaData()</code> calls. The statement is not executed. </td>
    </tr>
    <tr>
        <td class="notranslate"> @prof_start;<br> call&nbsp;hash('SHA256',&nbsp;'',&nbsp;1000000);<br> @prof_stop; </td>
        <td> Start/stop the built-in profiling tool. The top 3 stack traces of the statement(s) between start and stop are listed (if there are 3). </td>
    </tr>
    <tr>
        <td class="notranslate"> @prof_start;<br> @sleep 10;<br> @prof_stop; </td>
        <td> Sleep for a number of seconds. Used to profile a long running query or operation that is running in another session (but in the same process). </td>
    </tr>
    <tr>
        <td class="notranslate"> @transaction_isolation;<br> @transaction_isolation&nbsp;2; </td>
        <td> Display (without parameters) or change (with parameters 1, 2, 4, 8) the transaction isolation level. </td>
    </tr>
</tbody></table>

## 设置 H2 控制台

H2 控制台的设置信息存储在配置文件`.h2.server.properties`，该文件存放在你的用户目录下。在 Windows 上，用户目录通常是`C:\Documents and Settings\[username]` 或者 `C:\Users\[username]`。H2 控制台第一次启动时将自动创建应用所需要包含的配置文件。 支持的配置如下：

* webAllowOthers: 允许其他电脑连接
* webPort:  H2 控制台的端口
* webSSL: 使用加密的 TLS (HTTPS) 连接
*
除了这些设置,列出了最近使用连接的属性的格式 `<number>=<name>|<driver>|<url>|<user>` 使用转义字符 `\`。举例`1=Generic H2 (Embedded)|org.h2.Driver|jdbc\:h2\:~/test|sa`
