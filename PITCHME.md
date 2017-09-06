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

## fluendには難しいこと

* 大量の過去データをアップロードしたい。
* DBやストレージの移行
  * MySQL -> PostgreSQL
  * Local -> S3
* 内製スクリプト

---?image=assets/images/embulk-architecture.png&size=auto 70%

---?image=assets/images/EmbulkPlugins.png&size=auto 70%

---?image=assets/images/EmbulkSummary.png&size=auto 70%

---?image=assets/images/EmbulkBuiltin.png&size=auto 70%

---

<!-- .slide: class="two-floating-elements" -->
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



