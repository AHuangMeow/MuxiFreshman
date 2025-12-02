### 主线任务

**学习目标:**

1. 了解数据库的基本概念，了解关系型数据库和非关系型数据库的区别
2. 安装 MySQL (直接装物理机和docker都可以试试)
3. 学习数据库三范式+BCNF范式
4. 学习 SQL 基本语法和mysql基本使用（终端和可视化都要会）
    1. SQL 支持的数据类型（以 MySQL 版为主）
    2. 数据定义语言 DDL：
        - 数据库的创建、删除；
        - 对表的创建、删除和修改；
    3. 数据操纵语言 DML：
        - 表数据的增删改
        - 数据的查询（排序、过滤、数量限制等）
        - 表连接

---
### PostgreSQL

MySQL有些一言难尽，so这里用了PostgreSQL

安装：

```shell
sudo pacman -S postgresql
```

初始化数据库集群：

```shell
sudo -iu postgres initdb --locale $LANG -E UTF8 -D /var/lib/postgres/data
```

启动服务：

```shell
sudo systemctl start postgresql
sudo systemctl enable postgresql
```

切换用户为postgres:

```shell
sudo -iu postgres
```

---
### DDL

启动交互式管理shell：

```shell
psql
```

创建userdb数据库：

```sql
CREATE DATABASE userdb;
```

连接至数据库：

```text
\c userdb
```

创建users数据表：

```sql
CREATE TABLE users (
  username TEXT PRIMARY KEY,
  pswdhash TEXT NOT NULL
);
```

删除表：

```sql
DROP TABLE users;
```

删除数据库：

```sql
DROP DATABASE userdb;
```

---
### DML

#### 增：

```sql
INSERT INTO users (username, pswdhash) VALUES ('AHuang', 'hashvalue');
```

查看一下：

```sql
SELECT * FROM users WHERE username = 'AHuang';
```

```text
 username | pswdhash  
----------+-----------
 AHuang   | hashvalue
(1 行记录)
```

### 改：

```sql
UPDATE users SET pswdhash = 'newhashvalue' WHERE username = 'AHuang';
```

查看一下：

```sql
SELECT * FROM users WHERE username = 'AHuang';
```

```text
 username |   pswdhash   
----------+--------------
 AHuang   | newhashvalue
(1 行记录)
```

#### 删：

```sql
DELETE FROM users WHERE username = 'AHuang';
```

查看一下：

```sql
SELECT * FROM users;
```

```text
 username | pswdhash 
----------+----------
(0 行记录)
```

#### 查：

查询所有条目：

```sql
SELECT * FROM users;
```

```text
   username    |  pswdhash  
---------------+------------
 summonCreeper | hashvalue2
 AHuang        | hashvalue1
 Kebiao47      | hashvalue3
 HanHanAngel   | hashvalue4
(4 行记录)
```

条件过滤：

```sql
SELECT * FROM users WHERE username = 'AHuang';
```

```text
 username |  pswdhash  
----------+------------
 AHuang   | hashvalue1
(1 行记录)
```

升序：

```sql
SELECT * FROM users ORDER BY username ASC;
```

```text
   username    |  pswdhash  
---------------+------------
 AHuang        | hashvalue1
 HanHanAngel   | hashvalue4
 Kebiao47      | hashvalue3
 summonCreeper | hashvalue2
(4 行记录)
```

降序：

```sql
SELECT * FROM users ORDER BY username DESC;
```

```text
   username    |  pswdhash  
---------------+------------
 summonCreeper | hashvalue2
 Kebiao47      | hashvalue3
 HanHanAngel   | hashvalue4
 AHuang        | hashvalue1
(4 行记录)
```

数量限制：

```sql
SELECT * FROM users LIMIT 2;
```

```text
   username    |  pswdhash  
---------------+------------
 summonCreeper | hashvalue2
 AHuang        | hashvalue1
(2 行记录)
```

以上语句可组合使用