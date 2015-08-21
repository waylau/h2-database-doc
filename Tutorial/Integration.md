## 使用 Spring

### 使用 TCP 服务器

在 Spring 配置如下，来启动和关闭  H2 TCP 服务器

	<bean id = "org.h2.tools.Server"
	            class="org.h2.tools.Server"
	            factory-method="createTcpServer"
	            init-method="start"
	            destroy-method="stop">
	    <constructor-arg value="-tcp,-tcpAllowOthers,-tcpPort,8043" />
	</bean>

destroy-method 方法能防止热重部署和重启异常。

### 错误代码不兼容

有一个不相容，就是 Spring JdbcTemplate 和 H2 version 1.3.154版本及更新的版本,因为错误代码有变化。这将导致JdbcTemplate 不是检测重复的关键条件,所以一个 DataIntegrityViolationException 代替 DuplicateKeyException 抛出。参见 [SPR-8235](http://jira.spring.io/browse/SPR-8235) 的问题。解决方法是添加以下 XML 文件的根路径:

	<beans
	    xmlns="http://www.springframework.org/schema/beans"
	    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	    xsi:schemaLocation=
	        "http://www.springframework.org/schema/beans
	        http://www.springframework.org/schema/beans/spring-beans.xsd"
	    >
	    <import resource="classpath:org/springframework/jdbc/support/sql-error-codes.xml"/>
	    <bean id = "H2" class="org.springframework.jdbc.support.SQLErrorCodes">
	        <property name="badSqlGrammarCodes">
	            <value>
	                42000,42001,42101,42102,42111,42112,42121,42122,42132
	            </value>
	        </property>
	        <property name="duplicateKeyCodes">
	            <value>23001,23505</value>
	        </property>
	        <property name="dataIntegrityViolationCodes">
	            <value>22003,22012,22025,23000</value>
	        </property>
	        <property name="dataAccessResourceFailureCodes">
	            <value>90046,90100,90117,90121,90126</value>
	        </property>
	        <property name="cannotAcquireLockCodes">
	            <value>50200</value>
	        </property>
	    </bean>
	</beans>

## OSGi

标准的 H2 jar 可以下降为一捆在一个 OSGi 容器中。H2 实现了  OSGi Service Platform Release 4 Version 4.2 Enterprise Specification 中定义 JDBC 服务 。H2 数据源工厂服务注册以下属性:`OSGI_JDBC_DRIVER_CLASS=org.h2.Driver` 和 `OSGI_JDBC_DRIVER_NAME=H2`。`OSGI_JDBC_DRIVER_VERSION`属性反映了版本的驱动程序。

支持以下标准配置属性:`JDBC_USER, JDBC_PASSWORD, JDBC_DESCRIPTION, JDBC_DATASOURCE_NAME, JDBC_NETWORK_PROTOCOL, JDBC_URL, JDBC_SERVER_NAME, JDBC_PORT_NUMBER`。任何其他标准属性将被拒绝。非标准的属性将被传递给 H2 连接的 URL

## Java Management Extension (JMX)

支持 JMX 管理, 但不是默认启用。为了启用 JMX,追加 `;JMX=TRUE` 到数据库 URL ，当数据库打开时。各种工具支持 JMX,其中一个是 `jconsole` 工具。当打开 `jconsole` ,可以连接到打开的数据库的进程(使用服务器模式时,您需要连接到服务器进程)。然后去MBeans  部分。 在 `org.h2` 下每个数据库都会发现一个实体。这个实体对象名称的是数据库短名称,加上路径(每个冒号被替换为下划线字符)。

以下属性和操作的支持:

* CacheSize:当前使用的缓存大小，单位KB
* CacheSizeMax(读/写):最大缓存大小，单位KB
* Exclusive:这个数据库是否在独占模式
* FileReadCount:数据库打开后，文件读取操作的数量
* FileSize:文件大小，单位KB
* FileWriteCount:数据库打开后，文件写操作的数量
* FileWriteCountTotal:数据库创建后，文件写操作的数量
* LogMode(读/写):当前事务日志模式。有关详细信息,请参阅`SET LOG`
* Mode:兼容性模式(`REGULAR `如果没有使用兼容模式)
* MultiThreaded:如果启用了多线程就是 true
* Mvcc:如果启用了Mvcc 就是 true
* ReadOnly:如果数据库是只读的就是 true
* TraceLevel (读/写):文件跟踪级别
* Version:使用的数据库版本
* listSettings:列出数据库设置
* listSessions:开放会话列表,包括当前执行语句(如果有的话)和锁定的表(如果有的话)

要启用 JMX,可能需要设置 JVM 系统属性`com.sun.management.jmxremote` 和 `com.sun.management.jmxremote.port`