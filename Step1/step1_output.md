## 解答
テーブル: channels
---
|カラム名	|データ型	|NULL	|キー	|初期値	|AUTO INCREMENT|
| ---- | ---- | ---- | ---- | ---- | ---- |
|channel_id	|INT		||PRIMARY		||YES|
|chennel_name	|VARCHAR(20)	|	|INDEX	|	||
- ユニークキー制約: channel_name

テーブル: serieses
---
|カラム名	|データ型	|NULL	|キー	|初期値	|AUTO INCREMENT|
| ---- | ---- | ---- | ---- | ---- | ---- |
|series_id	|INT UNSIGNED	||PRIMARY		||YES|
|series_name	|VARCHER(80)	|	|	|	||

- ユニークキー制約: series_name

テーブル: genres
---
|カラム名	|データ型	|NULL	|キー	|初期値	|AUTO INCREMENT|
| ---- | ---- | ---- | ---- | ---- | ---- |
|genre_id	|INT		||PRIMARY		||YES|
|genre_name	|VARCHAR(20)	|	|	|	||
- ユニークキー制約: genre_name

テーブル: programs
---
|カラム名	|データ型	|NULL	|キー	|初期値	|AUTO INCREMENT|
| ---- | ---- | ---- | ---- | ---- | ---- |
|program_id	|INT		||PRIMARY		||YES|
|program_title	|VARCHAR(100)		||INDEX
|series_id	|INT UNSIGNED			|||0
|season_num	|INT UNSIGNED		|||0
|program_info	|VARCHAR(200)	|YES		|
|genre_id	|INT	|	|	|	||

外部キー制約:
- (series_id) REFERENSE serieses(series_id)
-	(genre_id) REFERENSE genres(genre_id)

テーブル: episords
---
|カラム名	|データ型	|NULL	|キー	|初期値	|AUTO INCREMENT|
| ---- | ---- | ---- | ---- | ---- | ---- |
|program_id	|INT||PRIMARY
|episord_num	|INT|		|PRIMARY
|episord_title	|VARCHAR(50)|		|INDEX
|episord_info	|VARCHAR(200)	|YES
|play_time	|TIME	|	|	|	||
- 外部キー制約: (program_id) REFERENSE programs(program_id)



テーブル: schedule
---
|カラム名	|データ型	|NULL	|キー	|初期値	|AUTO INCREMENT|
| ---- | ---- | ---- | ---- | ---- | ---- |
|channel_id	|INT||PRIMARY		|YES|
|start_time	|DATETIME||PRIMARY
|finish_time	|DATETIME
|program_id	|INT
|episord_num	|INT
|view_cnt	|INT	|	|	|0	||

外部キー制約:
- (channel_id) REFERENSE channels(channel_id)
- (program_id) REFERENSE programs(program_id)				|
