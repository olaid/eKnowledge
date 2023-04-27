# 2.PHPからスプレッドシートへの書き込み

## スプレッドシートの設定

スプレッドシートを作成しApps Scriptを開きます

![スクリーンショット 2021-12-10 8.36.22.png](2%20PHP%E3%81%8B%E3%82%89%E3%82%B9%E3%83%95%E3%82%9A%E3%83%AC%E3%83%83%E3%83%88%E3%82%99%E3%82%B7%E3%83%BC%E3%83%88%E3%81%B8%E3%81%AE%E6%9B%B8%E3%81%8D%E8%BE%BC%E3%81%BF%20096ea92ecd0940b69a2fa8944549190a/%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88_2021-12-10_8.36.22.png)

![スクリーンショット 2021-12-10 8.41.51.png](2%20PHP%E3%81%8B%E3%82%89%E3%82%B9%E3%83%95%E3%82%9A%E3%83%AC%E3%83%83%E3%83%88%E3%82%99%E3%82%B7%E3%83%BC%E3%83%88%E3%81%B8%E3%81%AE%E6%9B%B8%E3%81%8D%E8%BE%BC%E3%81%BF%20096ea92ecd0940b69a2fa8944549190a/%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88_2021-12-10_8.41.51.png)

## スクリプト書き込み

既存のmyFunction()に以下を上書きします

```jsx
function doPost(e) {
  // スプレッドシートへの書き込み
  var id = "10b7nSfhHdHSGBuGcWExLr7LuvmdBZgrdJugVX9LI6LU"
  var ss = SpreadsheetApp.openById(id)
  var spread = SpreadsheetApp.getActiveSpreadsheet();
  var lastRow = spread.getSheets()[0].getLastRow();
  var name = e.parameter.name;
  var age = e.parameter.age;
  spread.getSheets()[0].getRange((lastRow + 1), 1).setValue(name);
  spread.getSheets()[0].getRange((lastRow + 1), 2).setValue(age);
  spread.getSheets()[0].getRange((lastRow + 1), 3).setValue(e.parameter.query);
  // returnの設定
  var jsonData = {
    name: name,
    age: age,
    result: 'ok'
  }
  return ContentService.createTextOutput(JSON.stringify(jsonData))
    .setMimeType(ContentService.MimeType.JSON);
}
```

※きちんと保存すること

## デプロイ

デプロイから新しいデプロイを選択

![Untitled](2%20PHP%E3%81%8B%E3%82%89%E3%82%B9%E3%83%95%E3%82%9A%E3%83%AC%E3%83%83%E3%83%88%E3%82%99%E3%82%B7%E3%83%BC%E3%83%88%E3%81%B8%E3%81%AE%E6%9B%B8%E3%81%8D%E8%BE%BC%E3%81%BF%20096ea92ecd0940b69a2fa8944549190a/Untitled.png)

⚙からウェブアプリを選択します

アクセスできるユーザーを全員にします

![スクリーンショット 2021-12-10 8.48.38.png](2%20PHP%E3%81%8B%E3%82%89%E3%82%B9%E3%83%95%E3%82%9A%E3%83%AC%E3%83%83%E3%83%88%E3%82%99%E3%82%B7%E3%83%BC%E3%83%88%E3%81%B8%E3%81%AE%E6%9B%B8%E3%81%8D%E8%BE%BC%E3%81%BF%20096ea92ecd0940b69a2fa8944549190a/%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88_2021-12-10_8.48.38.png)

URLをコピーします

![Untitled](2%20PHP%E3%81%8B%E3%82%89%E3%82%B9%E3%83%95%E3%82%9A%E3%83%AC%E3%83%83%E3%83%88%E3%82%99%E3%82%B7%E3%83%BC%E3%83%88%E3%81%B8%E3%81%AE%E6%9B%B8%E3%81%8D%E8%BE%BC%E3%81%BF%20096ea92ecd0940b69a2fa8944549190a/Untitled%201.png)

## PHP

PCローカルに以下のPHPファイルを作成してください。

※$urlは上記で作成したURLを使用する

```php
<?php
// composer require fzaninotto/faker
// php faker_sam.php
require_once 'vendor/autoload.php';
 
$faker = Faker\Factory::create('ja_JP');

$data = [];
 
for ($i = 0; $i < 10; $i++) {
    $fname = $faker->name;
    if (preg_match("/^田/", $fname)){
        $data[$i] = [
            'name'     => $fname,            // 名前
            'age'      => $faker->numberBetween(1, 100),
        ];
    }
    else{
        $i -= 1;
    }
    
}
$url = 'https://script.google.com/macros/s/AKfycbw5jWHMr9sTip1Fp_OIvPUVtC3EvleaoVNm62zi67w5umuG83VQk3ccM7SSQWVRw5j4Bg/exec';
foreach($data as $rec){
    //implodeは配列要素を文字列により連結する
    //http_build_queryはURLエンコード
    $context = array(
        'http' => array(
            'method'  => 'POST',
            'header'  => implode("\r\n", array('Content-Type: application/x-www-form-urlencoded',)),
            'content' => http_build_query($rec)
        )
    );
    //stream_context_createはストリームコンテキストを作成する
    //file_get_contentsファイルの内容を全て文字列に読み込む
    $response_json = file_get_contents($url, false, stream_context_create($context));
    //JSON 文字列をデコードする
    $response_data = json_decode($response_json);
}

$query = ['query' => '=QUERY(A:B,"select A where B >50",-1)',];
$context = array(
    'http' => array(
        'method'  => 'POST',
        'header'  => implode("\r\n", array('Content-Type: application/x-www-form-urlencoded',)),
        'content' => http_build_query($query)
    )
);
$response_json = file_get_contents($url, false, stream_context_create($context));
$response_data = json_decode($response_json);

var_dump($response_data);
    
?>
```

PHPを実行しスプレッドシートに書き込まれることを確認してください

### 今回作成したものを使用して以下のものを作成してみてください

a.Fakerを英語に設定し英語名10名を標準出力にechoで出力する

b.aで出した1人目の名前と3文字目が一致する人物を5人スプレッドシートに出力してください

c.bで出した人たちの平均年齢をqueryを使用して出力してください