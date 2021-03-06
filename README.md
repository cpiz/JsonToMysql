# json2mysql 数据导入工具

[LeanCloud](https://leancloud.cn/)是一个简单方便的后端云服务，使用它的云引擎与云存储很容易搭建APP的后端服务。

但是它的云存储数据分析能力较弱，只能执行简单的HQL语句，执行效率也很低，如果能把数据导入到自己本地的MySql中就方便了。

LeanCloud也提供了数据导出服务，但数据格式是多行的Json对象，非常不利于导入Mysql这样的关系型数据库。

求索不利后，我写了这个数据导入工具，**同时也能用于其他服务Json to Mysql的导入**。

## 构建

使用Kotlin语言开发，用mysql-connector-java操作数据库

```bat
gradlew installDist
```

找到生成的bin和lib目录，拷贝到要存放的路径

## 使用

先根据LeanCloud中的表结构在本地MySql创建对应的数据库与表，为兼容emoji表情字符，建议使用utf8mb4编码(参考[mysql/Java服务端对emoji的支持](https://segmentfault.com/a/1190000000616820))。

```SQL
CREATE SCHEMA `mydb` DEFAULT CHARACTER SET = utf8mb4 COLLATE = utf8mb4_unicode_ci;

drop table if exists `mydb`.`mytable`;

CREATE TABLE `mydb`.`mytable` (
  `objectId` VARCHAR(30) NOT NULL,
  `avatarUrl` VARCHAR(150) NULL,
  `city` VARCHAR(45) NULL,
  `country` VARCHAR(45) NULL,
  `createdAt` VARCHAR(45) NULL,
  `emailVerified` VARCHAR(5) NULL,
  `gender` INT(2) NULL,
  `language` VARCHAR(45) NULL,
  `mobilePhoneVerified` VARCHAR(5) NULL,
  `db` VARCHAR(60) NULL,
  `nickName` VARCHAR(45) NULL,
  `province` VARCHAR(45) NULL,
  `updatedAt` VARCHAR(45) NULL,
  `username` VARCHAR(45) NULL,
  `authData` VARCHAR(150) NULL,
  PRIMARY KEY (`objectId`));
```

下载导出的数据文件，解压，执行下面的导入命令

```bat
json2mysql -u root -d mydb -t mytable -clear -i AuthData emailVerified mobilePhoneVerified -f d:\MyDocs\Desktop\part-00000
```

执行结果

```bat
data file line count: 11877
table `mytable` truncated
|>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>|100%
11874 rows processed, 11874 inserted, 9.3s
```

参数介绍

```bat
json2mysql [options...] arguments...
 -h VAL      : hostname (default: localhost)    # 数据库主机地址
 -P N        : port (default: 3306)             # 数据库连接端口
 -u VAL      : username (default: root)         # 数据库用户名
 -p VAL      : password (default: )             # 数据库密码
 -d VAL      : database (default: )             # 要导入的库名
 -t VAL      : table (default: )                # 要导入的表名
 -clear      : clear table before import (default: true) # 导入前是否清空表内数据
 -i STRING[] : ignore column name               # 要忽略的表字段，支持多个，用空格分开
 -b N        : batch commit (default: 2000)     # 每插入多少数据进行一次批量提交，太小会影响性能
 -f VAL      : file (default: )                 # 要导入的数据文件路径
```

## 其他

* 目前暂未支持字段的内容转换，大多数字段可用VARCHAR、INT支持
