# JSON

Created: 2022年3月7日 18:33

JavaScript Object Notation（ジャヴァスクリプト・オブジェクト・ノーテーション）の略

JavaScriptと相性が良い、ファイル形式

データやり取りで使用される。

次のデータ型に対応

・文字列
・数値
・null
・bool値
・オブジェクト
・配列

XMLのようにタグで囲っていないのでプログラム側で読み解く手間が少ない

近年ではJSON形式が主流

```json
[
  {"id" : "1", "name" : "tanaka"},
  {"id" : "2", "name" : "nakata"}
]
```

```xml
<?xml version="1.0" encoding="utf-8"?>
<data>
  <item>
    <id>1</id>
    <name>tanaka</name>
  </item>
  <item>
    <id>2</id>
    <name>nakata</name>
  </item>
</data>
```