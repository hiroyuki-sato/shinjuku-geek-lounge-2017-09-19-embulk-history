---

Hello, World!

---

## Test

| a | b |
|---|---|
| a | b |
| a | b |
| a | b |

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
````

  
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



