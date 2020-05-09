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
    # 查看帮助
    pt-online-schema-change --help

## 主要参数说明
- h 服务器
- P 数据库端口
- u 数据库用户
- p 数据库密码
- D 数据库名称
- t 表名称
- S socket
- --alter ddl语句部分
- --charset= 字符集
- dry-run 测试
- execute 执行

## 用法举例
    # 测试
    pt-online-schema-change --charset=utf8mb4  h=localhost,P=3306,u=root,p='',D=db1,t=t1,S='mysql.sock' --alter "ADD COLUMN REGISTERED_ADDRESS VARCHAR(128) NULL DEFAULT NULL COMMENT '注册地址' AFTER OPERATE_CHINESE_NAME" --dry-run
    # 运行
    pt-online-schema-change --charset=utf8mb4 h=localhost,P=3306,u=root,p='',D=db1,t=t1,S='mysql.sock'  --alter "ADD COLUMN REGISTERED_ADDRESS VARCHAR(128) NULL DEFAULT NULL COMMENT '注册地址' AFTER OPERATE_CHINESE_NAME" --execute


## 脚本实现举例
    #!/bin/bash

    cnn_host='127.0.0.1'
    cnn_user='user'
    cnn_pwd='password'
    cnn_socket='socket'
    cnn_db='database_name'
    cnn_table='table_name'
    
    alter='ADD COLUMN REGISTERED_ADDRESS VARCHAR(128)'

    pt-online-schema-change --charset=utf8mb4 h=${cnn_host},P=3306,u=${cnn_user},p=${cnn_pwd},D=${cnn_db},t=${cnn_table},S=${cnn_socket} --alter "${alter}" --execute


## 添加表字段 如添加表字段SQL语句为:

`ALTER TABLE `tb_test` ADD COLUMN `column1` tinyint(4) DEFAULT NULL;`
 
## 那么使用pt-online-schema-change则可以这样写

`sh pt.sh tb_test "ADD COLUMN column1 tinyint(4) DEFAULT NULL"`
 

 ## 修改表字段 SQL语句：

`ALTER TABLE `tb_test` MODIFY COLUMN `num` int(11) unsigned NOT NULL DEFAULT '0';`
 

## pt-online-schema-change工具

`sh pt.sh tb_test "MODIFY COLUMN num int(11) unsigned NOT NULL DEFAULT '0'"`
 

## 修改表字段名 SQL语句 

`ALTER TABLE `tb_test` CHANGE COLUMN age adress varchar(30);`

## pt-online-schema-change工具

`sh pt.sh tb_test "CHANGE COLUMN age address varchar(30)"`
 
## 添加索引 SQL语句 

`ALTER TABLE `tb_test` ADD INDEX idx_address(address);`
 
## pt-online-schema-change工具 

`sh pt.sh tb_test "ADD INDEX idx_address(address)"`



