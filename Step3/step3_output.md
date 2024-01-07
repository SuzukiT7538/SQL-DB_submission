## ステップ3

以下のデータを抽出するクエリを書いてください。

1. よく見られているエピソードを知りたいです。エピソード視聴数トップ3のエピソードタイトルと視聴数を取得してください
```sql
--実行したクエリ
SELECT
  ep.episord_title,
  SUM(sc.view_cnt)
FROM
  schedule AS sc
  INNER JOIN episords AS ep
    USING (program_id, episord_num)
GROUP BY
  sc.program_id,sc.episord_num
ORDER BY
  SUM(sc.view_cnt) DESC
LIMIT 3;
```
2. よく見られているエピソードの番組情報やシーズン情報も合わせて知りたいです。エピソード視聴数トップ3の番組タイトル、シーズン数、エピソード数、エピソードタイトル、視聴数を取得してください
```sql
SELECT
  pr.program_title,
  ep.episord_num,
  ep.episord_title,
  pr.season_num,
  SUM(sc.view_cnt)
FROM
  schedule AS sc
  INNER JOIN episords AS ep
    USING (program_id, episord_num)
  INNER JOIN programs AS pr
    USING (program_id)
GROUP BY
  sc.program_id,sc.episord_num
ORDER BY
  SUM(sc.view_cnt) DESC
LIMIT 3;
```
3. 本日の番組表を表示するために、本日、どのチャンネルの、何時から、何の番組が放送されるのかを知りたいです。本日放送される全ての番組に対して、チャンネル名、放送開始時刻(日付+時間)、放送終了時刻、シーズン数、エピソード数、エピソードタイトル、エピソード詳細を取得してください。なお、番組の開始時刻が本日のものを本日方法される番組とみなすものとします
```sql
--本日の日付は2024/01/07とします。
--実行したクエリ
SELECT
  ch.channel_name AS 'チャンネル名',
  sc.start_time AS '放送開始時刻',
  sc.finish_time AS '放送終了時刻',
  pr.season_num AS 'シーズン',
  ep.episord_title AS 'エピソードタイトル
  ',
  ep.episord_info AS 'エピソード詳細'
FROM
  schedule AS sc
  INNER JOIN channels AS ch
    USING (channel_id)
  INNER JOIN episords AS ep
    USING (program_id,episord_num)
  INNER JOIN programs AS pr
    USING (program_id)
WHERE
  sc.start_time BETWEEN '2024-01-07 00:00:00' AND '2024-01-07 23:59:59'
ORDER BY sc.start_time ASC;
```
4. ドラマというチャンネルがあったとして、ドラマのチャンネルの番組表を表示するために、本日から一週間分、何日の何時から何の番組が放送されるのかを知りたいです。ドラマのチャンネルに対して、放送開始時刻、放送終了時刻、シーズン数、エピソード数、エピソードタイトル、エピソード詳細を本日から一週間分取得してください
```sql
--ドラマチャンネルのチャンネルIDは5とします。
--実行したクエリ
SELECT
  sc.start_time AS '放送開始時刻',
  sc.finish_time AS '放送終了時刻',
  pr.season_num AS 'シーズン数',
  sc.episord_num AS 'エピソード数',
  ep.episord_title AS 'エピソードタイトル',
  ep.episord_info AS 'エピソード詳細'
FROM
  schedule AS sc
  INNER JOIN channels AS ch
    USING (channel_id)
  INNER JOIN episords AS ep
    USING (program_id, episord_num)
  INNER JOIN programs AS pr
    USING (program_id)
WHERE
  sc.channel_id = 3
  AND sc.start_time BETWEEN '2024-01-07 00:00:00' AND '2024-01-14 23:59:59'
ORDER BY
  sc.start_time ASC;
```
