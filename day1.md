# 学习SQL语句规则
1. 操作文件夹
    ```mysql
    create database db2;
    create database db2 default charset utf8;

    show databases;
    drop database db2;
    ```

2. 操作文件
    ```
    show tables;
    create table t1(id int, name char(10)) default charset=utf8;
    create table t1(id int, name char(10)) engine=innodb default charset=utf8;
    
    create table t1(
        列名 类型 null,  # null 表示可以为空
        列名 类型 not null # not null 表示不可为空
        列名 类型 not null default 1, # 表示默认为1，即不填的时候则为1
        列名 类型 not null auto_increment primary key, # auto_increment表示不需要填写，自增。约束：在mysql中，如果是自增的，则必须是一个key，key为索引

    ) engine=innodb default charset=utf8;
    
    # innodb 支持事务，原子性操作，创建表的时候需要设置引擎，创建数据库时不用
    # myisam 支持全局索引，存储快些
    # auto_increment 表示： 自增
    # primary key: 表示约束（不能重复且不能为空）；加速查找
    # 一个表只能有一个自增，一个primary key
    # not null: 不能为空
    ```


    + 数据类型：
        1. 数字：
            - tinyint[(m)] [unsigned] [zerofill]   
                有符号：-128 ～ 127.   
                无符号：0 ～ 255    
                特别的： MySQL中无布尔值，使用tinyint(1)构造。    
            - int[(m)][unsigned][zerofill]  
                整数，数据类型用于保存一些范围的整数数值范围：  
                有符号：-2147483648 ～ 2147483647  
                无符号：0 ～ 4294967295  
                特别的：整数类型中的m仅用于显示，对存储范围无限制。例如： int(5),当插入数据2时，select 时数据显示为： 00002  
            - bigint[(m)][unsigned][zerofill]  
                大整数，数据类型用于保存一些范围的整数数值范围： 
                有符号：-9223372036854775808 ～ 9223372036854775807  
                无符号：0  ～  18446744073709551615  

            - FLOAT[(M,D)] [UNSIGNED] [ZEROFILL]  
                0.00000000021545050000000  
            - DOUBLE[(M,D)] [UNSIGNED] [ZEROFILL]  
                0.00000000000000000014484  
            - FLOAT和DOUBLE都会存在精度损失，DOUBLE损失的小数位数比FLOAT靠后  
            - decimal[(m[,d])] [unsigned] [zerofill] (永远是精准的，因为它其实是用字符串来对数字进行存储)  
                decimal(10, 5)  # 第一个参数表示除了小数点外总共存储的数字个数，第二个参数表示小数点后存储的数字个数  

        2. 字符串：  
            char(10): 不管存储的够不够10个位置，都会占10个位置  查询数据快  
            varchar(10)： 存储几个字符就占几个位置  存储节省空间  
            PS（优化手段）:定长的往前放，变长的往后放  
            char跟varchar都最多只能存255个字符  
            text：保存变长的大字符串，最多可以有65536（2\**16-1）个字符  
            mediumtext：最多存储16,777,215(2\**24-1)个字符  
            longtext: 最多存储4,294,967,295(2\**32-1)个字符   
            如果longtext也存不下，则应该将数据存入文件中，然后将文件路径存入数据库中  


        3. 时间类型：  
            - DATE   
                YYYY-MM-DD（1000-01-01/9999-12-31）

            - TIME  
                HH:MM:SS（'-838:59:59'/'838:59:59'）

            - YEAR  
                YYYY（1901/2155）

            - DATETIME（最常用）  
                YYYY-MM-DD HH:MM:SS（1000-01-01 00:00:00/9999-12-31 23:59:59    Y）

            - TIMESTAMP  
                YYYYMMDD HHMMSS（1970-01-01 00:00:00/2037 年某时）

        4. 枚举类型和集合类型（用得很少，了解）  
            - enum 枚举类型  
                An ENUM column can have a maximum of 65,535 distinct elements. (The practical limit is less than 3000.)  
                示例：  
                ```
                CREATE TABLE shirts (
                    name VARCHAR(40),
                    size ENUM('x-small', 'small', 'medium', 'large', 'x-large')
                );
                INSERT INTO shirts (name, size) VALUES ('dress shirt','large'), ('t-shirt','medium'),('polo shirt','small');
                ```
            - set 集合类型  
                A SET column can have a maximum of 64 distinct members.  
                示例：  
                ```
                CREATE TABLE myset (col SET('a', 'b', 'c', 'd'));
                INSERT INTO myset (col) VALUES ('a,d'), ('d,a'), ('a,d,a'), ('a,d,d'), ('d,a,d');
                ```
                    
    + 主键通常写法    
    ```    
    create table t1(
        id int not null auto_increment primary key,
        name char(10)
    )engine=innodb default charset=utf8;
    ```
    + 清空表
    ```
    delete from t1; # 清空表后，插入数据的自增列在清空前的位置继续增加
    truncate table t1; # 清空表后，插入数据的自增列从1开始，并且该方法比delete要快得多
    ```
    + 删除表
    ```
    drop table t1;
    ```


3. 操作文件中的内容
    + 插入数据  
        `insert into t1(id, name) values(1, "egon");`
    + 删除  
        `delete from t1 where id<6;`
    + 修改  
        `update t1 set age=18 where age=17;`
    + 查看数据  
        `select * from t1;`

4. 外键：
    ```
    create table user_info(
        uid bigint auto_increment primary key,
        name varchar(32),
        department_id int,
        xxx char(10),
        constraint fk_user_depar foreign key ("department_id",) references department("id")  # constraint 约束
        constraint fk_user_depar2 foreign key ("xxx",) references XX("id")  # constraint 约束
    )engine=innodb default charset=utf8;

    create table department(
        id bigint primary key,
        title char(15)
    )engine=innodb default charset=utf8;
    ```

5. 补充
    + 主键  
    一个表只能有一个主键，主键可以由多列组成，其中的每一列都不能为空，并且多列的组合唯一
    ```
    CREATE TABLE t5 (
					  nid int(11) NOT NULL AUTO_INCREMENT,
					  pid int(11) not NULL,
					  num int(11),
					  primary key(nid,pid)  # 创建多列主键，都不能为空
					) ENGINE=InnoDB DEFAULT CHARSET=utf8;
    ```
    + 外键
    ```
    create table t6(
                    id int auto_increment primary key,
                    name char(10),
                    id1 int,
                    id2 int,
                    CONSTRAINT fk_t5_t6 foreign key (id1,id2) REFERENCES t5(nid,pid)
					)engine=innodb default charset=utf8;
    ```
    + 自增
    ```
    desc t10; # 显示字段信息
    # +-------+----------+------+-----+---------+----------------+
    # | Field | Type     | Null | Key | Default | Extra          |
    # +-------+----------+------+-----+---------+----------------+
    # | id    | int(11)  | NO   | PRI | NULL    | auto_increment |
    # | name  | char(10) | YES  |     | NULL    |                |
    # +-------+----------+------+-----+---------+----------------+
		
    show create table t10;
    
    show create table t10 \G;
    
    alter table t10 AUTO_INCREMENT=20;  # 修改接下来自增的起始编号
    ```
    + 自增步长
        - 基于会话级别：
        ```
        show session variables like 'auto_inc%';	查看全局变量
        set session auto_increment_increment=2; 	设置会话步长
        # set session auto_increment_offset=10; 
        ```
        - 基于全局级别：
        ```
        show global variables like 'auto_inc%';	    查看全局变量
        set global auto_increment_increment=2; 	    设置会话步长
        # set global auto_increment_offset=10;
        ```


