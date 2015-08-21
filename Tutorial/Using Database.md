## 使用 JDBC 连接数据库

JAVA应用要连接到数据库，首先需要加载数据库驱动，然后获得一个数据库连接，下面是一个简单的例子：

	import java.sql.*;
	public class Test {
	    public static void main(String[] a)
	            throws Exception {
	        Class.forName("org.h2.Driver");
	        Connection conn = DriverManager.
	            getConnection("jdbc:h2:~/test", "sa", "");
	        // add application code here
	        conn.close();
	    }
	}

代码中通过 `Class.forName(...)` 来加载驱动，通过`DriverManager.getConnection()`来打开一个连接，驱动名为"org.h2.Driver"。数据库 URL 总是使用`jdbc:h2:`来标识，`getConnection()` 的第二个参数是用户名（	`sa` 作为系统超级管理员的一个例子），第三个参数是密码，用户名是不区分大小写，但是密码是大小写区分的。

## 创建新一个新数据库

缺省情况下，如果 URL 指定的数据库并不存在，一个新的空的数据库将被自动的创建。创建数据库的用户自动成为这个数据库的超级管理员。

自动创建新库也可以通过特殊的 URL 进行屏蔽，参见[打开一个存在的数据库。](http://h2database.com/html/features.html#database_only_if_exists)

## 使用服务器模式

H2 目前支持三种服务器模式：web 服务器模式（H2 控制台）、TCP 服务器模式（C/S连接）和 PG 服务器模式（PostgreSQL 客户端）。可以通过多种方式启动服务器模式，通常的方式是通过`Server`工具。
开启服务器无需开启数据库——数据库会在客户端连接时开启

### 通过命令行启动 Server 工具

缺省设置下，输入下面命令并执行能启动 Server 工具：

	java -cp h2*.jar org.h2.tools.Server

通过下面的命令行，可以查看服务器启动命令行的参数及缺省值：

	java -cp h2*.jar org.h2.tools.Server -?

参数允许服务器工具启动到其他端口或者只是部分启动。

### 连接到 TCP 服务器

为了能远程通过 TCP 服务器访问数据库，使用下面的连接驱动和数据库URL：

•	JDBC驱动类： `org.h2.Driver`
•	数据库URL: `jdbc:h2:tcp://localhost/~/test`

关于数据库 URL，可以查看《特性》这章。注意不能通过浏览器访问这个 URL ，只能通过 H2 客户端（通过 JDBC）。

### 在应用内部启动TCP服务

在JAVA应用内部，也可以通过代码来实现TCP服务的启动和停止，例子代码如下：

	import org.h2.tools.Server;
	...
	// 启动 TCP Server
	Server server = Server.createTcpServer(args).start();
	...
	// 关闭 TCP Server
	server.stop();


### 从其他程序关闭 TCP 服务器

可以从另外的程序关闭 TCP 服务器，使用下面命令行：

	java org.h2.tools.Server -tcpShutdown tcp://localhost:9092

在用户应用中关闭服务器，使用:

	org.h2.tools.Server.shutdownTcpServer("tcp://localhost:9094");

这个功能将仅仅关闭 TCP 服务器。如果相同的程序有其他服务器，他们不会被关闭，而是继续执行。为了避免覆盖在数据库下次打开时，在调用这个方法时，所有的到数据库的连接将会关闭。要实现远程关闭服务器，需启用远程连接。关闭一个 TCP 服务器可以通过选项 `-tcpPassword` 来保护 (启动和关闭 TCP 服务器也要用这个密码)

## 使用 Hibernate

H2 数据库支持 Hibernate 3.1及以上的版本。 你能够使用 HSQLDB 方言，或是 H2 自己的方言。注意的是，在 Hibernate 中包含的 H2 方言有BUG，针对这些 BUG 的补丁已经被发布和修复,见<https://hibernate.atlassian.net/browse/HHH-3401>。你能够将它改名为H2Dialect.java，直接把它包含在你的应用中即可使用，或者使用 已经修复了这个问题的 Hibernate 的版本。

当使用 Hibernate,尝试使用 `H2Dialect` 如果可能的话。当使用`H2Dialect`,兼容性模式比如`MODE=MySQL`是不支持的。当使用兼容模式时,使用 Hibernate 相应的数据库的方言，而不是 `H2Dialect`;但请注意 H2 不支持所有数据库的所有功能。

## 使用 TopLink 和 Glassfish

在 Glassfish （或 Sun AS）中使用 H2，设置 Datasource Classname 为`org.h2.jdbcx.JdbcDataSource`。可以通过图形界面进行设置[Application Server] - [Resources] - [JDBC] - [Connection Pools], 或者编辑文件`sun-resources.xml`：修改元素`jdbc-connection-pool`，设置属性 `datasource-classname`为 `org.h2.jdbcx.JdbcDataSource`。

H2 数据库是兼容 HSQLDB 和 PostgreSQL。如果要使用 H2 的特殊属性，需要使用	`H2Platform`，源代码在`src/tools/oracle/toplink/essentials/platform/database/DatabasePlatform.java.txt`. 你将这个文件拷贝到你的应用中，并将它改名为 .java 的文件，并修改persistence.xml：

	<property
	    name="toplink.target-database"
	    value="oracle.toplink.essentials.platform.database.H2Platform"/>

旧版本的 Glassfish 的属性名为`toplink.platform.class.name`。

在 Glassfish 中使用 H2,拷贝 h2*.jar 到`glassfish/glassfish/lib` 目录


## 使用 EclipseLink

在 EclipseLink 使用 H2，可以通过类`org.eclipse.persistence.platform.database.H2Platform`。如果你使用的 EclipseLink 版本不支持，可以使用 OraclePlatform 替代，具体看 [H2Platform](http://wiki.eclipse.org/EclipseLink/Development/Incubator/Extensions/H2Platform)

## 使用 Apache ActiveMQ

当使用 H2 作为 Apache ActiveMQ 的后端数据库,请使用`TransactDatabaseLocker` 而不是默认的锁机制。否则,数据库文件将没有界限的增长。原因是默认锁机制是使用一个未提交的 `UPDATE` 事务,使得事务日志不会收缩（造成数据库文件增长)。`TransactDatabaseLocker` 使用 `SELECT ... FOR UPDATE` 而不是使用一个 `UPDATE` 语句,来解决问题。使用它,改变 ApacheMQ 配置元素`<jdbcPersistenceAdapter>`元素,属性`databaseLocker ="org.apache.activemq.store.jdbc.adapter.TransactDatabaseLocker"`。然而,使用 MVCC 模式将再次导致同样的问题。因此,在这种情况下,请不要使用 MVCC 模式。另一个(更危险)解决方案是`useDatabaseLock`设置为 false。

## 在 NetBeans 中使用 H2

项目 [H2 Database Engine Support For NetBeans](http://kenai.com/projects/nbh2) 允许您在 IDE 中启动和停止服务器 H2。

有一个已知问题当使用 Netbeans SQL Execution Window:在执行查询之前,另一个查询 `SELECT COUNT(*) FROM <query>` 运行。这是一个查询的问题会修改状态,如 `SELECT SEQ.NEXTVAL`。在这种情况下,两个序列值分配而不是一个。

## 在 jOOQ 中使用 H2 

jOOQ 添加一个 JDBC 薄层,允许 SQL 类型安全的建设,包括高级 SQL,存储过程和高级的数据类型。jOOQ 需要数据库模式作为基础代码生成。如果这是你的示例模式:

	CREATE TABLE USER (ID INT, NAME VARCHAR(50));

使用命令行来运行  jOOQ 代码生成器:

	java -cp jooq.jar;jooq-meta.jar;jooq-codegen.jar;h2-1.3.158.jar;.org.jooq.util.GenerationTool /codegen.xml
 
当 codegen.xml 在 classpath 并且包含了如下信息

	<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
	<configuration xmlns="http://www.jooq.org/xsd/jooq-codegen-2.3.0.xsd">
	    <jdbc>
	        <driver>org.h2.Driver</driver>
	        <url>jdbc:h2:~/test</url>
	        <user>sa</user>
	        <password></password>
	    </jdbc>
	    <generator>
	        <name>org.jooq.util.DefaultGenerator</name>
	        <database>
	            <name>org.jooq.util.h2.H2Database</name>
	            <includes>.*</includes>
	            <excludes></excludes>
	            <inputSchema>PUBLIC</inputSchema>
	        </database>
	        <generate></generate>
	        <target>
	            <packageName>org.jooq.h2.generated</packageName>
	            <directory>./src</directory>
	        </target>
	    </generator>
	</configuration>

使用生成的原件，可以执行如下查询s:

	Factory create = new H2Factory(connection);
	Result<UserRecord> result =
	create.selectFrom(USER)
	    .where(NAME.like("Johnny%"))
	    .orderBy(ID)
	    .fetch();

细节详见 [jOOQ 主页](http://www.jooq.org/) 和 [jOOQ Tutorial](http://www.jooq.org/tutorial.php)

## 在 Web 应用中使用 H2 数据库

在 Web 应用中使用数据库，可以有多种方式，这里有一些针对 Tomcat 和 JBoss 的例子。

### 内嵌模式

最简单（目前）的方法就是将数据库内嵌到应用中，这样就意味着应用启动的时候就打开了一个连接（好的办法是使用 Servlet 监听器，看下面的说明）。数据库能被多个 session 和应用访问，他们跟应用运行在一个进程内，大部分的 Servlet 容器只适用一个进程（如Tomcat），这些容器都是没有问题的（除非你使用集群）。Tomcat 使用多线程和多类加载器。如果多个应用同时访问同一个数据库，你需要将数据库的 jar 文件放在`shared/lib`或是`server/lib`目录。好的方案是 Web 应用启动时打开数据库，Web 应用停止时关闭数据库。如果是多个应用，只需要一个应用来处理启动和关闭。好的方案是一个 Session 一个连接，或者是一个请求（action）一个连接，连接使用完后尽可能的关闭它，当然不关闭并不会引起可怕的后果。

### 服务器模式

服务器模式是差不多的，但是它可以运行在其他的进程中。

### 使用 Servlet 监听去启动和停止数据库

增加 `h2*.jar` 文件到你的应用中，将下面的配置增加到你的 web.xml 中（在`filter`节下面的 `context-param`）：

	<listener>
	    <listener-class>org.h2.server.web.DbStarter</listener-class>
	</listener>

关于具体访问数据库的细节，你可以看 DbStarter.java。在这个工具中缺省打开的内嵌数据库 URL 为`jdbc:h2:~/test`， 用户名 sa，密码 sa。如果你要去使用这个连接，你可以使用下面的访问方式：

	Connection conn = getServletContext().getAttribute("connection");

DbStarter 也能够启动 TCP 服务，但是缺省状态下是不允许的。可以通过修改 web.xml 下的参数 `db.tcpServer` 来启用。下面是完整的配置选项，这些选项需要放在`description`标签和 `listener`/`filter` 标签中间：

	<context-param>
	    <param-name>db.url</param-name>
	    <param-value>jdbc:h2:~/test</param-value>
	</context-param>
	<context-param>
	    <param-name>db.user</param-name>
	    <param-value>sa</param-value>
	</context-param>
	<context-param>
	    <param-name>db.password</param-name>
	    <param-value>sa</param-value>
	</context-param>
	<context-param>
	    <param-name>db.tcpServer</param-name>
	    <param-value>-tcpAllowOthers</param-value>
	</context-param>

当 web 应用停止时，数据库连接将被自动关闭，如果通过 DbStarter 还启动了 TCP 服务，TCP 服务也将被自动关闭。

### 使用 H2 控制台 Servlet

H2 控制台是一个包含在 web 服务中的独立的应用，但是它也能作为一个servlet使用。为了做到这点，你需要将`h2*.jar`文件添加到你的应用中，在你的 web.xml 文件中增加下面的配置：

	<servlet>
	    <servlet-name>H2Console</servlet-name>
	    <servlet-class>org.h2.server.web.WebServlet</servlet-class>
	    <!--
	    <init-param>
	        <param-name>webAllowOthers</param-name>
	        <param-value></param-value>
	    </init-param>
	    <init-param>
	        <param-name>trace</param-name>
	        <param-value></param-value>
	    </init-param>
	    -->
	    <load-on-startup>1</load-on-startup>
	</servlet>
	<servlet-mapping>
	    <servlet-name>H2Console</servlet-name>
	    <url-pattern>/console/*</url-pattern>
	</servlet-mapping>

关于更多的细节，请参考`src/tools/WEB-INF/web.xml`

要创建一个合适的 H2 控制台的 web  应用，运行下面的命令：
	
	build warConsole


## Android

Android 设备(使用Dalvik VM)上可以使用这个数据库代替 SQLite。到目前为止,只有很少的测试和基准测试运行,但似乎性能类似于 SQLite,除了打开和关闭数据库没有优化 (H2大约需要0.2秒,和SQLite约0.02秒)。读操作似乎比 SQLite 快,而写操作慢点。到目前为止,只有很少的测试运行,一切似乎正常工作。全文搜索还没有测试,然而本地的全文搜索应该能工作。

使用 H 2而不是 SQLite 原因是:

* 全 Unicode 支持包括 UPPER() 和 LOWER()
* 流 API 用于 BLOB 和 CLOB 数据
* 全文搜索
* 多连接
* 用于定义方法和触发器
* 数据库文件加密
* 读写 CSV 文件 (该功能也可以在数据库外部使用)
* 引用完整性和检查约束
* 更好的数据类型和 SQL 支持
* 内存数据库、只读数据库、表关联
* 与其他数据库更好的兼容,简化应用程序移植
* 可能更好的性能（对读操作）
* 服务器模式(在不同的机器上通过 TCP/IP 访问同一个数据库)。
 
目前只支持 JDBC API (计划在将来的版本中支持 Android 数据库API)。常规的 H2 jar文件和小 `h2small-*.jar` 可以使用。为了创建更小的 jar 文件,运行命令`./build.sh jarSmall` (Linux / Mac OS) 或 `build.bat jarSmall` (Windows)

数据库文件需要存储在一个可以被一个应用程序访问的地方。例子:

	String url = "jdbc:h2:/data/data/" +
	    "com.example.hello" +
	    "/data/hello" +
	    ";FILE_LOCK=FS" +
	    ";PAGE_SIZE=1024" +
	    ";CACHE_SIZE=8192";
	Class.forName("org.h2.Driver");
	conn = DriverManager.getConnection(url);
	...

限制:使用连接池目前不支持,因为所需的javax.sql 类是不在 Android 上的。

## CSV (Comma Separated Values) 支持

CSV（逗号分隔文件）文件在数据库系统中支持`CSVREAD`和`CSVWRITE`方法，也可以把它作为数据库之外的一个工具来使用。
将数据库查询结果写成CSV文件

### 通过数据库读取 CSV 文件

使用 `CSVREAD`，例子：

	SELECT * FROM CSVREAD('test.csv');

请注意由于性能原因,`CSVREAD` 不应使用在 jion 内部。相反,先导入数据(可能到一个临时表),如果有必要创建所需的索引,然后查询这个表。

### 通过 CSV 文件导入数据

从CSV文件快速加载或导入数据(有时称为“批量加载”)的方法是结合表创建和导入。可选地,该列名称和数据类型可以在创建表时设置。另一个选项 `INSERT INTO ... SELECT`。

	CREATE TABLE TEST AS SELECT * FROM CSVREAD('test.csv');
	CREATE TABLE TEST(ID INT PRIMARY KEY, NAME VARCHAR(255))
	    AS SELECT * FROM CSVREAD('test.csv');

### 通过数据库写 CSV 文件

使用 `CSVWRITE`,

	CREATE TABLE TEST(ID INT, NAME VARCHAR);
	INSERT INTO TEST VALUES(1, 'Hello'), (2, 'World');
	CALL CSVWRITE('test.csv', 'SELECT * FROM TEST');

### 通过 Java 应用写 CSV 文件

使用 Csv 工具而无需数据库，

	import java.sql.*;
	import org.h2.tools.Csv;
	import org.h2.tools.SimpleResultSet;
	public class TestCsv {
	    public static void main(String[] args) throws Exception {
	        SimpleResultSet rs = new SimpleResultSet();
	        rs.addColumn("NAME", Types.VARCHAR, 255, 0);
	        rs.addColumn("EMAIL", Types.VARCHAR, 255, 0);
	        rs.addRow("Bob Meier", "bob.meier@abcde.abc");
	        rs.addRow("John Jones", "john.jones@abcde.abc");
	        new Csv().write("data/test.csv", rs, null);
	    }
	}

### 通过 Java 应用读 CSV 文件

读 CSV 文件可以无需打开数据库，

	import java.sql.*;
	import org.h2.tools.Csv;
	public class TestCsv {
	    public static void main(String[] args) throws Exception {
	        ResultSet rs = new Csv().read("data/test.csv", null, null);
	        ResultSetMetaData meta = rs.getMetaData();
	        while (rs.next()) {
	            for (int i = 0; i < meta.getColumnCount(); i++) {
	                System.out.println(
	                    meta.getColumnLabel(i + 1) + ": " +
	                    rs.getString(i + 1));
	            }
	            System.out.println();
	        }
	        rs.close();
	    }
	}