---

# Embulkの歴史
### 過去・現在、これから
### 佐藤 博之 @hiroysato

---

## 2015年2月

* バルクローダーが必要だった。
* 要件
  * 入力: apacheのログファイル
  * 出力: PostgreSQL
* Embulkを見つける => よさそう

---

## Embulk(エンバルク)

![](assets/images/Embulk.png)

---

## Embulk(エンバルク)

* fluentdの開発者が開発
* 2015年1月リリース
* fluentdのバッチ版
* fluentdでは難しいことを解決するために開発

---

## fluentdには難しいこと

* 大量の過去データをアップロードしたい。
* DBやストレージの移行
  * MySQL -> PostgreSQL
  * Local -> S3
* 日次バッチ処理内製スクリプト
  * 実は誰かが書いたか分からないスクリプトがたまに失敗していて困る。

---?image=assets/images/embulk-architecture.png&size=auto 70%

---

## Embulk

* プラグインアーキテクチャ
  * JRubyとJava(Scala/Kotlin)
* 並行・並列処理
* guess
* リトライ・レジューム
* yaml設定

---?image=assets/images/EmbulkPlugins2.png&size=auto 70%

---

## Embulk(Plugin type)

| type      | 説明                     |
|-----------|--------------------------|
| input     | データの取得(file,s3)    |
| decoder   | 圧縮ファイルの伸張(gzip) |
| parser    | CSVデータのパース        |
| filter    | データの加工不要行の抽出 |
| exec      | バルクロード分散実行     |

---

## Embulk Plugin type2

| type      | 説明                     |
|-----------|--------------------------|
| formatter | データの整形(CSV)        |
| encoder   | データの圧縮(gzip)       |
| output    | データの出力(file,s3)    |


---

## プラグインの数

| Type     | Num| Type     |Num |
|----------|----|----------|----|
| Input    | 52 | Decoder  | 3  |
| Output   | 47 | Filter   | 9  |
| Filter   | 57 | Encoder  | 5  |
| Parser   | 31 | Executor | 3  |


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

| Embulk    | 説明       |
|-----------|------------|
| boolean   | 真偽値     |
| long      | 整数型     |
| timestamp | 時刻       |
| double    | 浮動小数点 |
| string    | 文字列     |

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

---?image=assets/images/EmbulkPCap1.png&size=auto 70%

---?image=assets/images/EmbulkPCap2.png&size=auto 70%

---

## 直面する問題

* このままでは、このソフトは使えない。
* 今後一般的になりそう
* 他の人がプラグインを作ってくれるのを待つ
* ドキュメントぐらいなら..

---?image=assets/images/EmbulkSummary.png&size=auto 70%

---

## Embulkのまとめ

* http://qiita.com/hiroysato/items/397f36c4838a0a93e352
* Embulkのまとめ(2015/2/16)
* Embulk関連のリンク集
* 国内最大の情報源に
* まとまっている？
* もはや検索した方が早いかも

---?image=assets/images/EmbulkPlugins.png&size=auto 70%

---

## Embulkプラグインのまとめ

* http://qiita.com/hiroysato/items/da45e52fb79c39547f69
* 当初はEmbulkのまとめに掲載
* リンクが増えたので独立
* オフィシャルのプラグインページが公開(2015/2/26)

---?image=assets/images/EmbulkBuiltin.png&size=auto 70%

---

## 組み込みプラグイン覚書

* http://qiita.com/hiroysato/items/a71669d3e5be2049c238
* 組込プラグインの説明(2015/2/28)
* 英訳してPR、本体のドキュメントベースに
* 組込プラグインの説明
* ソースを見てドキュメント化


---?image=assets/images/EmbulkCmdRef.png&size=auto 70%

---

## 組込コマンドヘルプ

* http://qiita.com/hiroysato/items/86ad1cdfb1754440eed5
* 2015/11/10
* 未英訳

---

## 待望のDBプラグイン

* embulkプロジェクトからリリース
* embulk-input-jdbc (2015/2/16)
* embulk-output-jdbc (2015/2/16)
* 対応データベース
  * JDBC/MySQL/PostgreSQL/RedShift
* mode
  * replace/insert

---

## 様々な人々の貢献

---

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
* embulk-filter-flatten_json(2015/10/11)
* embulk-filter-json_key(2015/10/29)
* embulk-filter-to_json(2016/1/22)

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

---?image=assets/images/Groonga.png&size=auto 70%

---

## イベント開催・RubyBiz

* Embulk meetup Tokyo#2(2015/12/15)
* RubyBiz 第一回グランプリ受賞(Fluentd/Embulk)
* 徐々に利用事例が増える。

---

## 様々な要望と対応

* 設定ファイルの共有化 
  * Liquid Embulk 0.7.7 ~ (2015/10/28)
* ArrayやHashデーターの対応
  * Embulk 0.8.0 (2016/1/13)
* ワークフロー連携 

--- 

## 設定ファイル共有化:Liquid

* Embulk 0.7.7(2015/10/28)
* 環境変数の読み込み可能に


---

## 設定ファイル共有化:Liquid

```yaml
in: 
{% if env.EMBULK_ENV == 'production' %}
  {% include 'db/prod' %}
{% else %}
  {% include 'db/dev' %}
{% endif %}
{% include 'query/query' %}
out:
  type: stdout
```

```yaml
#
  type: postgresql
  host: 127.0.0.1
  user: username
  password: password
  database: embulk_prod
```

---

## Array,Hash対応:type: json

| Embulk    | 説明       |
|-----------|------------|
| boolean   | 真偽値     |
| long      | 整数型     |
| timestamp | 時刻       |
| double    | 浮動小数点 |
| string    | 文字列     |
| json      | JSON(NEW)  |

---

## ワークフロー:digdag

* 2016/06/15登場
* ワークフローエンジン
* YAML(風)の記述で依存関係の記述ができる
* ローカルモード・サーバーモードがある。
  * ローカルでテスト、サーバで本番という作業ができる。

---

## その頃

* embulk-filter-null_string(2016/8/19)
  * ""をnullに変換
* embulk-filter-calc(2016/8/19)
  * 四則演算
* embulk-parser-jsonpath(2016/8/29)
  * jsonパーサー(Java)

---?image=assets/images/Digdag.png&size=auto 70%

---

## Digdagまとめ&プラグイン

* Digdagプラグインのまとめ(2016/6/15~)
* プラグイン作成
  * digdag-plugin-ssh
  * digdag-plugin-mysql

---

## 初めての雑誌掲載

* WEB+DB PRESS Vol.92(2016/4/23)
* Embulkよるバッチデータ収集(古橋さん)
* DeNAでのデータ収集(瀬尾さん)

---

## 一休CTO 伊藤さんの紹介

* Web+DB PRESS Vol.94(2016/8/24)
* OSSによるデータ分析基盤の構築
  * Embulk, Re:dash, digdag
* Rebuid#152
  * Excelプラグイン脚光を浴びる

---

## 最近の改善

* 複雑なブートストラップ
  * .sh/.bat => Java(.jar) => JRuby(command line) => Java(EmbulkEmbed) => JRuby(plugin bootstrap) => Java/JRuby(per plugin)
  * v0.8.21 ~ v0.8.32でJavaに書き直し
* REST clientプラグイン支援ライブラリ
  * embulk-base-restcilent
  * REST apiとの連携が容易に

---?image=assets/images/Timestamp.png&size=auto 50%

---

## 最近の速度改善

* タイムスタンプパースの速度問題
  * [原因調査](https://github.com/embulk/embulk/issues/145) JRuby遅い
  * JRubyのTimestampParser JRuby -> Java
  * Embulk 0.8.27での導入 2017/7/9
  * 4倍以上の高速化
  * JRubyでも取り込まれる予定(9.2.0.0)

---

## smdmtsさん

* EMRを使って大量のデータ処理
* 様々なプラグインを作成
* 詳しくはこの後

---

## これから

* Java7 -> Java8
* バイナリタイプ
* Pure Java Plugin
* Reporter Plugin [#700](https://github.com/embulk/embulk/pull/700)
  * ロードした件数などがわかるように

---?image=assets/images/Book.png&size=auto 70%

---

* データ分析基盤構築入門
  * [Fluentd，Elasticsearch，Kibanaによるログ収集と可視化]
  * Embulkプラグイン辞典付き
  * 技術評論社
  * 2017年9月21日発売
  * ISBN 978-4-7741-9218-5

---

# Thank you!

---

## 参考資料

* [並列データ転送ツール『Embulk』リリース！](http://frsyuki.hatenablog.com/entry/2015/02/16/080150)
* [Embulk, an open-source plugin-based parallel bulk data loader](https://www.slideshare.net/frsyuki/embuk-making-data-integration-works-relaxed)


