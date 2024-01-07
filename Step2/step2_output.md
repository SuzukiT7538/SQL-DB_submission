## 解答
### 手順１. データベースを作成する
まずは次のクエリを使って internet_tv データベースを作成します。
```sql
--実行したクエリ
CREATE DATABASE internet_tv;

--実行結果
Query OK, 1 row affected (0.01 sec)
```

データベースの作成が完了したら、ちゃんとできているか確認してみましょう。
```sql
--実行したクエリ
SHOW DATABESES;

--実行結果
+--------------------+
| Database           |
+--------------------+
| information_schema |
| internet_tv        | -- ←internet_tvデータベースが
| mysql              | --  新たに生まれました！
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.00 sec)
```
データベースの生成が確認出来たら、次の手順でテーブルが作成しやすいようにinternet_tvを選択しましょう。
```sql
--実行したクエリ
USE internet_tv

--実行結果
Database changed
```
---
### 手順２. 必要なテーブルを作成する
手順1で作成したinternet_tvデータベースに、テーブルを作成していきます。
まずは現在のinternet_tvの状態を確認しておきましょう。
```sql
--実行したクエリ
SHOW TABLES;

--実行結果
Empty set (0.00 sec)
```
internet_tvデータベースが空の状態であることが確認できたので、テーブルを作っていきます。
各テーブルは次の３つの手順で作成します。
1. テーブルを作成する
2. テーブルが生成されたかを確認する
3. 生成されたテーブルの構造を確認する

#### 2-1. テーブルを作成する
テーブルの作成は、
1. 外部キーとして参照される親ファイル
2. 参照する子ファイル

の順で行います。

まずはchannelsテーブルを作成します。
テーブルの作成には`CREATE TABLE`構文を使用します。
```sql
--実行したクエリ
CREATE TABLE channels (
  channel_id   INT         NOT NULL PRIMARY KEY AUTO_INCREMENT,
  channel_name VARCHAR(30) NOT NULL UNIQUE
);

--実行結果
Query OK, 0 rows affected (0.05 sec)
```
#### 2-2.テーブルが生成されたかを確認する
クエリが正常に実行できたら、テーブルがちゃんと生成されたか確認してみましょう。
テーブルの確認には`SHOW TABLES`構文を使用します。
```sql
--実行したクエリ
SHOW TABLES;

--実行結果
+-----------------------+
| Tables_in_internet_tv |
+-----------------------+
| channels              |
+-----------------------+
1 row in set (0.00 sec)
```
internet_tvデータベースに最初のテーブルが誕生しました！

#### 2-3. 生成したテーブルの構造を確認する
テーブルが生成されたことは確認できましたが、まだテーブルの中身が正しく作成されているかは不安なままです。
テーブルの中身が正常であるかも確認しておきましょう！

テーブルの構造を確認するには`SHOW COLUMNS`構文を使用します。
```sql
--実行したクエリ
SHOW COLUMNS FROM channels;

--実行結果
+--------------+-------------+------+-----+---------+----------------+
| Field        | Type        | Null | Key | Default | Extra          |
+--------------+-------------+------+-----+---------+----------------+
| channel_id   | int         | NO   | PRI | NULL    | auto_increment |
| channel_name | varchar(20) | NO   | UNI | NULL    |                |
+--------------+-------------+------+-----+---------+----------------+
2 rows in set (0.01 sec)
```
これでchannelsテーブルが完成したことを確認できました！
この調子でどんどん作成していきましょう！

```sql
--seriesテーブルの作成
CREATE TABLE serieses (
  series_id INT UNSIGNED NOT NULL PRIMARY KEY AUTO_INCREMENT,
  series_name VARCHAR(80) NOT NULL UNIQUE
);
```
```sql
--genresテーブルの作成
CREATE TABLE genres (
  genre_id   INT         NOT NULL PRIMARY KEY AUTO_INCREMENT,
  genre_name VARCHAR(20) NOT NULL UNIQUE
);
```
```sql
--programsテーブルの作成
CREATE TABLE programs (
  program_id     INT           NOT NULL  PRIMARY KEY  AUTO_INCREMENT,
  program_title  VARCHAR(100)  NOT NULL,
  series_id      INT UNSIGNED          NOT NULL  DEFAULT 0,
  season_num     INT UNSIGNED          NOT NULL  DEFAULT 0,
  program_info   VARCHAR(200),
  genre_id       INT           NOT NULL,
  INDEX (program_title),
  FOREIGN KEY series_id(series_id) REFERENCES serieses(series_id),
  FOREIGN KEY genre_id(genre_id) REFERENCES genres(genre_id)
);
```
```sql
--episordsテーブルの作成
CREATE TABLE episords (
  program_id     INT           NOT NULL  AUTO_INCREMENT,
  episord_num    INT           NOT NULL,
  episord_title  VARCHAR(50)   NOT NULL,
  episord_info   VARCHAR(200),
  play_time      TIME          NOT NULL,
  published_date DATETIME     NOT NULL,
  PRIMARY KEY (program_id, episord_num),
  INDEX (episord_title),
  FOREIGN KEY program_id(program_id) REFERENCES programs(program_id)
);
```
```sql
--scheduleテーブルの作成
CREATE TABLE schedule (
  channel_id   INT       NOT NULL  AUTO_INCREMENT,
  start_time   DATETIME  NOT NULL,
  finish_time  DATETIME  NOT NULL,
  program_id   INT       NOT NULL,
  episord_num  INT       NOT NULL,
  view_cnt     INT       NOT NULL,
  PRIMARY KEY (channel_id, start_time),
  FOREIGN KEY channel_id(channel_id) REFERENCES channels(channel_id),
  FOREIGN KEY program_id(program_id) REFERENCES channels(channel_id)
);
```
これで必要なすべてのテーブルを作成することができました！

---
### 手順3. サンプルデータの作成、読み込み
---

今回、サンプルデータはcsvファイルを作成して読み込ませるようにします。
サンプルデータの作成もテーブルの作成同様、
1. 外部キーとして参照される親ファイル
2. 参照する子ファイル

の順で作成します。

#### 3-1. channels_data.csvの作成

Step３で使用するクエリを見ると、ドラマチャンネルが必要です。
また、複数のチャンネルでの再放送を再現するためにドラマチャンネル2、そして同様にしてアニメチャンネル、アニメチャンネル２を作成します。また、映画作品を流すチャンネルとして、映画チャンネルも作成します。
[[作成したchannles_data.csv]](./channels_data.csv)

SQLにデータを読み込ませる準備をします。
準備1:一旦mysql空ログアウトし、 `--enable-local-infile`オプションを付けて同じユーザーでログインします。
準備2: `SET GLOBAL local_infile = 1;`
を使用し、、ローカルファイルを読み込める状態になります。



読み込みの準備ができたら、実際にデータを読み込ませましょう！
```sql
--実行したクエリ
LOAD DATA LOCAL INFILE '/var/www/html/Step2/channels_data.csv'
  INTO TABLE channels
  FIELDS TERMINATED BY ','
  LINES TERMINATED BY '\n'
  IGNORE 1 ROWS
    (channel_id,channel_name);

--実行結果
Query OK, 5 rows affected (0.00 sec)
Records: 5  Deleted: 0  Skipped: 0  Warnings: 0
```
読み込みが完了したら、念のためテーブル内のレコードを表示してみましょう！
```sql
--実行したクエリ
SELECT
  *
FROM
  channels;

--実行結果
+------------+--------------+
| channel_id | channel_name |
+------------+--------------+
|          1 | 'anime'      |
|          2 | 'anime02'    |
|          3 | 'drama'      |
|          4 | 'drama02'    |
|          5 | 'movie'      |
+------------+--------------+
5 rows in set (0.00 sec)
```
無事にデータが登録されていました！
この調子で他のサンプルデータも作成していきましょう！

[[serieses_data.csv]](./serieses_data.csv)

```sql
--serieses_data.csvの読み込み
LOAD DATA LOCAL INFILE '/var/www/html/Step2/serieses_data.csv'
  INTO TABLE serieses
  FIELDS TERMINATED BY ','
  LINES TERMINATED BY '\n';
```
[[genres_data.csv]](./genre_data.csv)
```sql
--genre_data.csvの読み込み
LOAD DATA LOCAL INFILE '/var/www/html/Step2/genre_data.csv'
  INTO TABLE genres
  FIELDS TERMINATED BY ','
  LINES TERMINATED BY '\n';
```
[[programs_data.csv]](./programs_data.csv)
```sql
--programs_data.csvの読み込み
LOAD DATA LOCAL INFILE '/var/www/html/Step2/programs_data.csv'
  INTO TABLE programs
  FIELDS TERMINATED BY ','
  LINES TERMINATED BY '\n';
```

[[episord_data.csv]](./episords_data.csv)
```sql
--episords_data.csvの読み込み
LOAD DATA LOCAL INFILE '/var/www/html/Step2/episords_data.csv'
  INTO TABLE episords
  FIELDS TERMINATED BY ','
  LINES TERMINATED BY '\n';
```
[[schedule_data.csv]](./schedule_data.csv)
schedulesテーブルのサンプルデータは、
作成した5つのチャンネルに対して、2024年1月1日から2024年1月17日までのデータを作成しました。
```sql
--schedule_data.csvの読み込み
LOAD DATA LOCAL INFILE '/var/www/html/Step2/schedule_data.csv'
  INTO TABLE schedule
  FIELDS TERMINATED BY ','
  LINES TERMINATED BY '\n';

```
これでinternet_tvデータベースのすべての実装が完了しました！
