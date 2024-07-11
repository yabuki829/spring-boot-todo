# springbootを使用したTODOアプリのメモ

##  MySQLの起動
```
brew services start mysql@8.0
```
## MySQLの停止
```
brew services stop mysql@8.0
```
## MySQLに接続する
```
mysql -u root -p
# パスワードを入力する
Enter password: 
```
## データベースの作成
```
CREATE DATABASE [your_database_name];
CREATE USER '[your_username]'@'localhost' IDENTIFIED BY '[your_password]';
GRANT ALL PRIVILEGES ON [your_database_name].* TO '[your_username]'@'localhost';
FLUSH PRIVILEGES;
```

## データベースが作成されているか確認
```
SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| todo_db            | <--- 作成されている
+--------------------+
```
## テーブルの作成
```
USE your_database_name;
-> Database changed


# 今回はtodo_itemsを作成する
id, title, done_flg, time_limit 
CREATE TABLE todo_items (
    id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(40) NOT NULL,
    done_flg TINYINT(1) DEFAULT 0,
    time_limit DATE
);
Query OK, 0 rows affected, 1 warning (0.01 sec)
```
## テーブルが作成されているか確認
```
SHOW TABLES;
+-------------------+
| Tables_in_todo_db |
+-------------------+
| todo_items        |
+-------------------+
1 row in set (0.00 sec)


DESCRIBE todo_items;
+------------+-------------+------+-----+---------+----------------+
| Field      | Type        | Null | Key | Default | Extra          |
+------------+-------------+------+-----+---------+----------------+
| id         | int         | NO   | PRI | NULL    | auto_increment |
| title      | varchar(40) | NO   |     | NULL    |                |
| done_flg   | tinyint(1)  | YES  |     | 0       |                |
| time_limit | date        | YES  |     | NULL    |                |
+------------+-------------+------+-----+---------+----------------+
4 rows in set (0.01 sec)



```


## spring.applicationの設定
```
spring.application.name=todo

# MySQLの接続情報
spring.datasource.url=jdbc:mysql://localhost:3306/todo_db
spring.datasource.username=todo_user
spring.datasource.password=todo_pass

# HikariCP（デフォルトの接続プール）の設定
spring.datasource.hikari.connection-timeout=20000
spring.datasource.hikari.maximum-pool-size=10

# JPA（Java Persistence API）の設定
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL8Dialect

```

## spring.datasource.url
MySQLデータベースへのJDBC URLを指定します。
localhost:3306はMySQLサーバがローカルホストのポート3306で動作していることを示します。your_database_nameを実際のデータベース名をかく。
## spring.datasource.username:
MySQLデータベースにアクセスするためのユーザー名を指定する

# spring.datasource.password:
上記のユーザー名に対応するパスワードを指定する。

# spring.datasource.hikari.connection-timeout:
接続プールの設定で、接続が確立されるまでの最大待ち時間をミリ秒単位で指定する。

# spring.datasource.hikari.maximum-pool-size:
接続プールの最大サイズを指定する
# spring.jpa.hibernate.ddl-auto:
Hibernateがデータベーススキーマを管理する方法を指定します。
updateは、既存のスキーマを保持しつつ、新しいエンティティやフィールドが追加された場合にテーブルを更新します。
他のオプション:create, create-drop, validate
# spring.jpa.show-sql:
trueに設定すると、Hibernateが生成するSQLクエリがコンソールに表示されます。

# spring.jpa.properties.hibernate.dialect:
Hibernateが使用するSQL方言を指定します。
MySQL 8.0を使用している場合、org.hibernate.dialect.MySQL8Dialectを指定します。
