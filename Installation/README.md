安装
====

## 环境

运行本数据库，需要安装下面软件。其他软件可能也可以，但没有测试过。

### 数据库引擎

* Windows XP 或者 Vista, Mac OS X, 或者 Linux
* Sun Java 6 或更新
* 推荐用 Windows 文件系统： NTFS (FAT32 只支持文件最大为 4 GB)

### H2 控制台

* Mozilla Firefox

## 支持的平台

本数据库是Java 编写的，故可以跨平台使用。已经在 Java 6 和 7 上做了测试。当前，数据库的开发和测试已经在使用了 Java 6 的 Windows 8 和  Mac OS X ，但仍可以运行在其他系统和其他的 Java 。支持所有的主流操作系统(Windows XP, Windows Vista, Windows 7, Mac OS, Ubuntu,...) 

## 安装软件

运行 installer  或者解压安装包到任意目录

## 目录结构

安装后，目录结构如下：

Directory |	Contents
---- | ----
bin	| JAR 和批处理文件
docs | 文档
docs/html |	HTML 页面
docs/javadoc | Javadoc 文件
ext	| 外部依赖 (构建时下载的)
service	| 运行数据库作为 Windows 的 Service
src	| 源文件
src/docsrc	| 文档源文件
src/installer |	安装程序,shell和发布的构建脚本
src/main | 数据库引擎源代码
src/test | 测试源代码
src/tools | 工作和数据库适配器源代码