## 升级、备份和恢复

### 数据库升级

数据库升级的推荐方案是，老版本的数据库的数据备份成 SQL 脚本的方式，在新版本的数据库上执行这些 SQL 来恢复数据。

### 使用 Script 工具备份数据

备份数据库有多种方式。如可以直接拷贝数据库文件，但是不建议在数据库在使用的时候去拷贝文件，另外数据库文件是二进制的，不能直接读懂，并且数据库文件可能会比较大，推荐的备份方式是创建压缩的 SQL 脚本文件，并且 H2 提供了数据导出的 `Script` 工具：

	java org.h2.tools.Script -url jdbc:h2:~/test -user sa -script test.zip -options compression zip

也可能通过 SQL 命令`SCRIPT`去备份数据库，关于更多的命令选项，请查看 SQL 命令`SCRIPT`。备份也能通过远程来做，但是文件被创建在服务器上，你可以通过 FTP 服务获取备份的脚本文件。

### 用脚本恢复数据

从一个SQL脚本文件恢复数据库，你可以使用`RunScript`工具：

	java org.h2.tools.RunScript -url jdbc:h2:~/test -user sa -script test.zip -options compression zip

关于更多的命令的选项，请参考 SQL 命令 `RUNSCRIPT`。恢复也能通过远程来实现，但是恢复的文件需要在服务器上。可以通过FTP服务器上传恢复需要的脚本文件。也可以通过`RUNSCRIPT`执行 SQL 脚本，SQL 脚本文件内也可以引用另外的 SQL 脚本文件，在服务器模式下，也可以远程执行 SQL 脚本，但是要求脚本文件和被引用的脚本未见都在服务器上。

### 在线备份

`BACKUP` SQL 语句和 `Backup` 工具都能创建全库的备份文件的压缩 zip 包。但是，这个文件的内容并不可读

相比脚本方式，BACKUP命令并不锁定数据库对象，也不阻塞用户，但是 BACKUP 命令备份结果是事务一致的。：
	
	BACKUP TO 'backup.zip'

`Backup`工具 (org.h2.tools.Backup)不能创建在线备份；程序在执行的时候，数据库不能使用。

并不支持数据库运行的同时创建数据库备份，除非是文件系统支持创建快照，但是快照不能保证数据拷贝顺序的正确性。