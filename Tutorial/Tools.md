## 命令行工具

H2数据库提供了一组命令行工具，如果你需要了解这些工具，使用参数`-?`，如：

	java -cp h2*.jar org.h2.tools.Backup -?

命令行工具有：

* Backup 创建数据库备份
* ChangeFileEncryption 允许改变文件加密密码和数据库的加密算法
* Console 启动基于浏览器的 H2 控制台
* ConvertTraceFile 转换 `.trace.db` 文件到 Java 应用和 SQL 脚本
* CreateCluster 从一个独立的数据库服务创建集群
* DeleteDbFiles 删除所有的数据库文件
* Recover 恢复损坏的数据库
* Restore 从数据库备份中恢复数据库
* RunScript 运行数据库 SQL 脚本
* Script 为数据库备份或迁移导出 SQL 脚本
* Server 启动 H2 服务模式
* Shell 命令行工具

这些工具也能在程序中通过调用相应的方法来使用，相关详细的调用说明，请参考 JavaDoc 文档。

## Shell 工具

Shell 工具是最简单的命令行工具，开始时，输入:

	java -cp h2*.jar org.h2.tools.Shell

你会要求输入数据库URL、JDBC 驱动、用户名和密码。连接设置也可以作为命令行参数设置。连接后,你会得到的列表选项。内置命令不需要以分号结束,但只是执行 SQL 语句,需要以分号 `;`作为结束。这允许输入多行语句:

	sql> select * from test
	...> where id = 0;

默认情况下,结果是打印成表。结果有许多列,考虑使用模式列表:

	sql> list
	Result list mode is now on
	sql> select * from test;
	ID  : 1
	NAME: Hello
	
	ID  : 2
	NAME: World
	(2 rows, 0 ms)

## 使用 OpenOffice 基础框架

OpenOffice.org 基础框架支持通过 JDBC 连接数据库。你也可以通过 OpenOffice 框架连接到 H2 数据库。首先将 JDBC 驱动增加到 OpenOffice 中，下面的步骤可以连接到H2数据库：

* 启动 OpenOffice Writer，进入[Tools], [Options]
* 确认你在 OpenOffice.org/Java 中选择了 JAVA 运行环境
* 单击 [Class Path...], [Add Archive...]
* 选择你的 h2 的 jar 文件（本机上的路径可以由你选择）
* 单击 [OK] (按要求的点击), 停止OpenOffice (包括 Quickstarter)
* 启动 OpenOffice 框架
* 连接到一个存在的数据库；选择 [JDBC]; [Next]
* 输入数据库URL，如： `jdbc:h2:~/test`
* 输入 JDBC 驱动类： `org.h2.Driver`

你可以访问存于用户当前目录的数据库。

使用 H2 数据通过 NeoOffice (去掉 X11 的 OpenOffice)：

* 在 NeoOffice，到[NeoOffice]， [Preferences]
* 在[NeoOffice]页下找到[Java]
* 单击[Class Path], [Add Archive...]
* 选择 h2 的 jar 文件（本地目录，任你选择合适的目录）
* 单击[OK]（根据需要），重启 NeoOffice

现在，你可以通过"Database Wizard"创建一新的数据库：

* 单击[File], [New], [Database]
* 选择[Connect to existing database]，并且选择[JDBC]，单击next
* 输入数据源的URL，例如：`jdbc:h2:~/test`
* JDBC驱动类： org.h2.Driver

其他的在 NeoOffice 中使用 H2 的方法：

* 将 H2 的 jar 包打包到一个扩展包中
* 在 NeoOffice 中作为扩展 Java 包进行安装

这个能通过使用 NetBeans OpenOffice 插件来创建。详细看[Extensions Development](http://wiki.services.openoffice.org/wiki/Extensions_development_java)

## Java Web Start / JNLP

当使用 Java Web Start / JNLP（JAVA网络加载协议），允许访问标签必须被设置在 .jnlp 文件，并且 .jar 的应用文件必须被签名，否则，当你试着去写文件系统，下面的异常将会被抛出：`java.security.AccessControlException`: 拒绝访问 (`java.io.FilePermission ... read`). 如访问标签：

	<security>
	    <all-permissions/>
	</security>

## 使用连接池

如果 H2 的数据库已经打开，打开一个连接很快。如果要打开和关闭许多连接，使用连接池，可以提升性能。H2 包含了一个简单的连接池，它是基于Christian d'Heureuse 的 [Mini Connection Pool Manager](http://www.source-code.biz/snippets/java/8.htm) 。还有其他更复杂的开源连接池可以使用，如[Apache Commons DBCP](http://jakarta.apache.org/commons/dbcp/)。H2 从内置连接池获取连接比使用`DriverManager.getConnection()`快两倍左右。内置的连接池使用方法如下：

	import java.sql.*;
	import org.h2.jdbcx.JdbcConnectionPool;
	public class Test {
	    public static void main(String[] args) throws Exception {
	        JdbcConnectionPool cp = JdbcConnectionPool.create(
	            "jdbc:h2:~/test", "sa", "sa");
	        for (int i = 0; i < args.length; i++) {
	            Connection conn = cp.getConnection();
	            conn.createStatement().execute(args[i]);
	            conn.close();
	        }
	        cp.dispose();
	    }
	}

## 全文检索

H2 包含了两种全文检索的实现。一种通过使用 Apache Lucene 来实现，另一种是通过存储索引文件到数据库里的一张特殊表来实现的（私有实现）。

### 使用私有全文检索

通过下面的调用来实现初始化：

	CREATE ALIAS IF NOT EXISTS FT_INIT FOR "org.h2.fulltext.FullText.init";
	CALL FT_INIT();

如果你要用到私有全文检索，你需要在每个数据库里都初始化它。然后你能创建一张表用于全文检索的索引，如：

	CREATE TABLE TEST(ID INT PRIMARY KEY, NAME VARCHAR);
	INSERT INTO TEST VALUES(1, 'Hello World');
	CALL FT_CREATE_INDEX('PUBLIC', 'TEST', NULL);

PUBLIC 是 schema 的名字， TEST 是索引表名。字段名列表是可选的，在上面的例子中，所有的字段都被索引，索引的更新是实时的，使用下面的查询语句可以进行搜索：

	SELECT * FROM FT_SEARCH('Hello', 0, 0);

下面的语句将得到一个指定内容的搜索结果集。

	QUERY: "PUBLIC"."TEST" WHERE "ID"=1

删除表上的索引

	CALL FT_DROP_INDEX('PUBLIC', 'TEST');

要得到原始的数据，需要使用 `FT_SEARCH_DATA('Hello', 0, 0);`。结果包含字段 SCHEMA (schema名), TABLE (表名), COLUMNS (字段名数组), 和 KEYS (对象数组)。可以和表做连接，使用连接如下：`SELECT T.* FROM FT_SEARCH_DATA('Hello', 0, 0) FT, TEST T WHERE FT.TABLE='TEST' AND T.ID=FT.KEYS[0];`

你也能在 Java 应用中使用索引：

	org.h2.fulltext.FullText.search(conn, text, limit, offset);
	org.h2.fulltext.FullText.searchData(conn, text, limit, offset);

### 用 Lucene 实现的全文检索

使用 Lucene 实现的全文检索，你需要将 Lucene 加入到 classpath 中，并且处理相关的依赖，如果你使用 H2 控制台，你能增加 Lucene 的 jar 文件到环境变量 `H2DRIVERS` 或 `CLASSPATH` 中。通过下面的语句初始化 Lucene 全文检索：

	CREATE ALIAS IF NOT EXISTS FTL_INIT FOR "org.h2.fulltext.FullTextLucene.init";
	CALL FTL_INIT();

如果你需要是使用 Lucene 全文检索，你每个数据库都需要初始化。你也能通过下面的语句创建全文索引表：

	CREATE TABLE TEST(ID INT PRIMARY KEY, NAME VARCHAR);
	INSERT INTO TEST VALUES(1, 'Hello World');
	CALL FTL_CREATE_INDEX('PUBLIC', 'TEST', NULL);

PUBLIC 是 schema 名, TEST 是表名. 字段名列表是可选的，在上面的例子中，所有的字段都被索引，索引的更新是实时的，使用下面的查询语句可以进行搜索：

	SELECT * FROM FTL_SEARCH('Hello', 0, 0);

下面的语句将得到一个指定内容的搜索结果集：

	QUERY: "PUBLIC"."TEST" WHERE "ID"=1

表上的索引(警告称,这将对所有的整个数据库的全文索引进行检索):

	CALL FTL_DROP_INDEX('PUBLIC', 'TEST');

要得到原始的数据，需要使用 `FTL_SEARCH_DATA('Hello', 0, 0);`。结果包含字段 SCHEMA (schema名), TABLE (表名), COLUMNS (字段名数组), 和KEYS (对象数组)。可以和表做连接，使用连接如下`SELECT T.* FROM FTL_SEARCH_DATA('Hello', 0, 0) FT, TEST T WHERE FT.TABLE='TEST' AND T.ID=FT.KEYS[0]`;

你也能在JAVA应用中使用索引：

	org.h2.fulltext.FullTextLucene.search(conn, text, limit, offset);
	org.h2.fulltext.FullTextLucene.searchData(conn, text, limit, offset);

Lucene 搜索支持全文搜索仅在特定的列。列名必须大写(如果原始列双援引除外)。为列名下划线(_),另一个强调需要添加。例子:

	CREATE ALIAS IF NOT EXISTS FTL_INIT FOR "org.h2.fulltext.FullTextLucene.init";
	CALL FTL_INIT();
	DROP TABLE IF EXISTS TEST;
	CREATE TABLE TEST(ID INT PRIMARY KEY, FIRST_NAME VARCHAR, LAST_NAME VARCHAR);
	CALL FTL_CREATE_INDEX('PUBLIC', 'TEST', NULL);
	INSERT INTO TEST VALUES(1, 'John', 'Wayne');
	INSERT INTO TEST VALUES(2, 'Elton', 'John');
	SELECT * FROM FTL_SEARCH_DATA('John', 0, 0);
	SELECT * FROM FTL_SEARCH_DATA('LAST_NAME:John', 0, 0);
	CALL FTL_DROP_ALL();

Lucene 全文搜索实现内部并不是同步的。如果你同时更新数据库和查询全文搜索(直接使用 Java API 的 H2 或 Lucene 本身),您需要确保操作正确同步。如果不是这样的话,你可能会有异常 `org.apache.lucene.store.AlreadyClosedException: this IndexReader is closed`。
