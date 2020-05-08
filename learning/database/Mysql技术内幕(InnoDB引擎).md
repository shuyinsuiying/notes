# 第一章 MySQL体系结构和存储引擎

## 1.1 定义数据库和实例

> **数据库**： 物理操作系统文件或其他形式文件类型的集合。在MySQL中，数据库文件可以是frm，MYD，MYI结尾的文件。
>
> **实例**：MySQL数据库由后台线程以及一个共享内存区组成。共享内存可以被运行的后台线程所共享。数据库梳理才是真正用于操作数据库文件的。

​	MySQL被设计成一个单进程多线程架构的数据库，与SQL Server类似但与Oracle多进程的架构有所不同。即：MySQL数据库实例在系统上的表现就是一个进程。

​	当启动实例时，MySQL数据库回去读取配置文件，根据配置文件的参数来启动数据库实例。与Oracle的参数文件(spfile)类似，但是MySQL配置文件可以是缺省的。MySQL数据库按照 /etc/my.cnf &Rightarrow; /etc/mysql/my.cnf &Rightarrow;  /usr/local/mysql/my.cnf &Rightarrow; ~/.my.cnf  的顺序读取配置文件的。并且MySQL会以读到的最后一个配置文件内的参数为准















