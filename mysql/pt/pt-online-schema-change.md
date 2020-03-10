# 在线修改大表结构工具 pt-online-schema-change 
## 使用场景
在线数据库的维护中，总会涉及到研发修改表结构的情况，修改一些小表影响很小，而修改大表时，往往影响业务的正常运转，如表数据量超过500W，1000W，甚至过亿时
## pt-online-schema-change 简介
pt-online-schema-change是percona公司开发的一个工具，在percona-toolkit包里面可以找到这个功能，它可以在线修改表结构
## 原理
- 首先它会新建一张一模一样的表，表名一般是_new后缀
- 然后在这个新表执行更改字段操作
- 然后在原表上加三个触发器，DELETE/UPDATE/INSERT，将原表中要执行的语句也在新表中执行
- 最后将原表的数据拷贝到新表中，然后替换掉原表
## 好处
- 降低主从延时的风险
- 可以限速、限资源，避免操作时MySQL负载过高
## 建议
- 在业务低峰期做，将影响降到最低

## pt-online-schema-change安装
    yum install percona-toolkit-2.2.11-1
    # 或者
    rpm -ivh percona-toolkit-2.2.11-1.noarch.rpm
    # 查看安装的结果
    rpm -ql percona-toolkit-2.2.11-1
    # 可能依赖的包
    yum install perl-DBI
    yum install perl-DBD-MySQL
    yum install perl-Time-HiRes
    yum install perl-IO-Socket-SSL

## pt-online-schema-change使用











