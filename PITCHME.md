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


---?image=assets/images/EmbulkPlugins.png&size=auto 70%

---?image=assets/images/EmbulkSummary.png&size=auto 70%

---?image=assets/images/EmbulkBuiltin.png&size=auto 70%

---?image=assets/images/EmbulkBuiltin.png&size=auto 70%

## Embulk

* item
* item 
* item 

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

## Embulk type

| Embulk    | 説明       | Ruby      | Java 
|-----------|------------|-----------|------
| boolean   | 真偽値     | Boolean   | Boolean
| long      | 整数型     | Integer   | Long
| timestamp | 時刻       | Time      | Timestamp
| double    | 浮動小数点 | Float     | Double 
| string    | 文字列     | String    | String 
| json      | JSON       | *確認中*  | *確認中*


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



