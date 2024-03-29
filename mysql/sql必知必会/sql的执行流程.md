# sql的执行流程

## oracle执行sql语句流程

- **语法检查**: 检查语句语法是否有错误
- **语义检查**: 检查访问对象是否存在，比如数据库名，表名，字段名是否存在
- **权限检查**: 是否有操作数据库，表，字段的权限，是否有更改，插入数据的权限
- **共享池检查**: 一块内存池，缓存sql语句和语句的执行计划。通过sql语句的hash，查找有没有该语句的执行计划，如果有直接执行，如果没有就创建解析树，生成执行计划
- **优化器**: sql语句的硬解析，根据sql语句创建解析树，生成执行计划
- **执行器**: 根据执行计划，执行sql语句



## mysql执行sql语句流程

- **查询缓存**: 缓存命中直接返回，没有就进入解析起->优化器生成执行计划，再缓存sql的结果，mysql8.0已被废弃
- **解析器**: 进行语法分析和语义分析
- **优化器**: 生成该语句的执行计划，是全表扫描还是走索引
- **执行器**: 执行前判断该用户有没有操作权限，具备权限就执行该语句，返回后并缓存该执行计划

## mysql常见的存储引擎
- **InnoDB**: 5.5默认的存储引擎，支持事务，行级锁定，外键约束
- **MyISAM**: 5.5之前默认的存储引擎，不支持事务和外键约束，速度快，占用资源少
- **Memory**: 使用系统内存作为存储媒介，更快的速度响应，数据是临时才使用，mysqld进程崩溃时，数据丢失
- **NDB**: 用作mysql cluster分布式环境
- **Archive**: 用户文件归档，有良好的压缩机制，请求进来时会进行压缩