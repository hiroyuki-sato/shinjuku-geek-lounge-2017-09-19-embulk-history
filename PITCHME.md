---

Hello, World!!

---

## Test

| a | b |
|---|---|
| a | b |
| a | b |
| a | b |

---

## Embulk(エンバルク)

* fluendの開発者が開発
* 2015年1月リリース
* fluentdのバッチ版
* fluentdでは難しいことを解決するために開発

---

## fluendには難しいこと

* 大量の過去データをアップロードしたい。
* DBやストレージの移行
  * MySQL -> PostgreSQL
  * Local -> S3
* 内製スクリプト

---?image=assets/images/embulk-architecture.png&size=auto 70%

---

## Embulk

| type      | 説明                     |
|-----------|--------------------------|
| input     | データの取得(file,s3)    |
| decoder   | 圧縮ファイルの伸張(gzip) |
| parser    | CSVデータのパース        |
| filter    | データの加工不要行の抽出 |
| exec      | バルクロード分散実行     |
| formatter | データの整形(CSV)        |
| encoder   | データの圧縮(gzip)       |
| output    | データの出力(file,s3)    |

---

## YAML設定

```yaml
in:
  type: file
  parser: csv
  decoders:
  - type: gzip
filters:
  - type: calc
out:
  type: s3
  formatter: jsonl
  encoders:
  - type: gzip
exec:
```
---
  
## Embulk type

| Embulk    | 説明       | Ruby      | Java 
|-----------|------------|-----------|------
| boolean   | 真偽値     | Boolean   | Boolean
| long      | 整数型     | Integer   | Long
| timestamp | 時刻       | Time      | Timestamp
| double    | 浮動小数点 | Float     | Double 
| string    | 文字列     | String    | String 


---

## その頃

* 2015年2月、バルクローダーが必要だった。
* 要件
  * 入力: apacheのログファイル
  * 出力: PostgreSQL
* Embulkを見つける => よさそう

---

## 現実

* コンセプトは良かったが出たばかりだった。
* apacheのログ => プラグインなし
* PostgreSQLなし => プラグインなし
* ドキュメント => Quick start 程度

---

## できたこと

* CSVのパース
* プラグイン
  * embulk-plugin-redis (2015/1/27)
  * embulk-plugin-vim (2015/1/28)
  * embulk-plugin-input-pcapng-files (2015/1/28)
  * embulk-output-elasticsearch (2015/2/15)
  * embulk-input-s3 (2015/2/16)

---

## Embulkのまとめ

* Embulkのまとめ(2015/2/16)
* Embulk関連のリンク集
* 国内最大の情報源に
* まとまっている？
* もはや検索した方が早いかも

## Embulkプラグインのまとめ

* 当初はEmbulkのまとめに掲載
* リンクが増えたので独立
* オフィシャルのプラグインページが公開(2015/2/26)

## Embulk組み込みプラグイン覚書

* 組込プラグインの説明(2015/2/28)
* 英訳してPR、本体のドキュメントベースに
* 組込プラグインの説明
* ソースを見てドキュメント化

## Embulk組込コマンドヘルプまとめ

* 2015/11/10

---?image=assets/images/EmbulkPlugins.png&size=auto 70%

---?image=assets/images/EmbulkSummary.png&size=auto 70%

---?image=assets/images/EmbulkBuiltin.png&size=auto 70%

---?image=assets/images/EmbulkBuiltin.png&size=auto 70%

## 待望のDBプラグイン

* embulk-input-jdbc (2015/2/16)
* embulk-output-jdbc (2015/2/16)
* 対応データベース
  * JDBC/MySQL/PostgreSQL/RedShift
* mode
  * replace/insert

## sakamaさん

* embulk-output-bigquery
  * Google BigQuery用プラグイン
  * 2015/3/17
* 組込CSVフォーマッターの修正(2015/3/13)
* その他
  * sftp(input/output),azure_blob_storage等
* そのままコミッターに

---

## hito4_tさん

* 2015年3月頃登場
* JDBC周りの大幅な回収
* Oracle/SQLServerのプラグイン追加
* そのままメンテナーに

---

## civitaspoさん

* JSON周りのプラグインを作成
* embulk-filter-to_json(2016/1/22)
* embulk-filter-flatten_json(2015/10/11)
* embulk-filter-json_key(2015/10/29)

---

## sonotsさん

* embulk後の定番フィルターを作成
* embulk-filter-row(2015/6/17)
* embulk-filter-column(2015/6/23)
* embulk-filter-timestamp_format(2016/3/19)
* embulk-filter-typecast(2016/4/27)
* embulk-output-bigqueryリライト
* 等々


---

## その頃

* embulk-parser-apache-log(2015/5/30)
  * 誰も作ってくれなかったので仕方なく自分で
* embulk-output-groonga(2015/12/6)
  * 全文検索用Groonga用プラグイン
* embulk-parser-sisimai(2016/2/18)
  * バウンスメール解析sisimaiのパーサー

---

## イベント開催・RubyBizグランプリ

* Embulk meetup Tokyo#2(2015/12/15)
  * 
* RubyBiz 第一回グランプリ受賞(Fluentd/Embulk)

---

## 様々な要望

* 設定ファイルの共有化
* ArrayやHashデーターの対応
* ワークフロー連携

--- 

## 設定ファイル共有化:Liquid

---

## Array,Hash対応:type: json

| Embulk    | 説明       | Ruby      | Java 
|-----------|------------|-----------|------
| boolean   | 真偽値     | Boolean   | Boolean
| long      | 整数型     | Integer   | Long
| timestamp | 時刻       | Time      | Timestamp
| double    | 浮動小数点 | Float     | Double 
| string    | 文字列     | String    | String 
| json      | JSON       | *確認中*  | *確認中*

---

## ワークフロー:digdag


---

## その頃

* Digdagプラグインのまとめ
* embulk-filter-null_string(2016/8/19)
* embulk-filter-calc(2016/8/19)
* embulk-parser-jsonpath(2016/8/29)


---

##

* 伊藤さんによるredash連携の紹介
* Excelからのデータ読み込みが注目されるように

---

## 最近の改善

* 複雑なブートストラップ
  * .sh/.bat => Java(.jar) => JRuby(command line) => Java(EmbulkEmbed) => JRuby(plugin bootstrap) => Java/JRuby(per plugin)
* タイムスタンプパースの速度問題
  * JRubyパースの速度
* REST clientプラグイン支援ライブラリ


## これから

* Java7 -> Java8
* バイナリタイプ
* Pure Java Plugin
* Reporter Plugin [#700](https://github.com/embulk/embulk/pull/700)

## その他

---

## Embulk

* プラグインアーキテクチャ
  * JRubyとJava(Scala/Kotlin)
* 並行・並列処理
* guess
* リトライ・レジューム
* yaml設定

---

## Embulk type

| Embulk    | 説明       | Ruby      | Java 
|-----------|------------|-----------|------
| boolean   | 真偽値     | Boolean   | Boolean
| long      | 整数型     | Integer   | Long
| timestamp | 時刻       | Time      | Timestamp
| double    | 浮動小数点 | Float     | Double 
| string    | 文字列     | String    | String 


---


---

## Test

### 日本語

* item
  * item
* item
* item

---

## hoge

```yaml
in:
  type: file
  parser: csv
  decoders:
  - type: gzip
filters:
  - type: calc
out:
  type: s3
  formatter: jsonl
  encoders:
  - type: gzip
exec:
```
  
  
---

## 様々な要望

* 設定を共通化したい。
* Arrayやハッシュのサポート

---

## 様々な要望

* Liquidによる設定の共通化
* type: jsonのサポート

---

## Liquid

```yaml
in:
  {% include 'auth' %}
```

_auth.yml

```yaml
type: mysql
user: xxx
password: xxx
```

---

## その頃...

* json(path)プラグインのjava化
* embulk-filer-calc
  * 四則演算
* filter-null_string:
  * ヌル変換
* fluendの正規表現互換パーサー
  * parser-joni_regexp


  
---

## JRuby利用の弊害

* 起動が遅い
* ソースが複雑
* 時刻のパースが遅い

---

## TimeStampParserの速度改善

* [原因調査](https://github.com/embulk/embulk/issues/145)
* JRubyのTimestampParser JRuby -> Java
* Embulk 0.8.27での導入 2017/7/9
* 4倍以上の高速化
* ソースが複雑
* 時刻のパースが遅い



