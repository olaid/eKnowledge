# 1.Fakerを用いた擬似データ作成

# Windows

手順に従ってインストールしてください

## phpインストール

[https://www.php.net/downloads](https://www.php.net/downloads)

## composerインストール

[https://weblabo.oscasierra.net/php-composer-windows-install/](https://weblabo.oscasierra.net/php-composer-windows-install/)

- 下記エラーの解決法（composerインストール中のエラー）
Composer needs the following settings to be enabled in your php.ini file.
Required settings:
The php.ini used by your command-line PHP is: C:\php-8.1.0\php.ini
    
    php.iniファイルの
    
    ;extension_dir = "ext"を
    
    extension_dir = "C:\php-8.1.0\ext"
    
    に変更する
    

# Mac

homebrewはインストール済みを前提としています

## phpインストール

brew install php

![スクリーンショット 2021-12-09 21.41.24.png](1%20Faker%E3%82%92%E7%94%A8%E3%81%84%E3%81%9F%E6%93%AC%E4%BC%BC%E3%83%86%E3%82%99%E3%83%BC%E3%82%BF%E4%BD%9C%E6%88%90%20b766baca56034008ba830b4879d3a0e2/%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88_2021-12-09_21.41.24.png)

php -v

![スクリーンショット 2021-12-09 22.23.14.png](1%20Faker%E3%82%92%E7%94%A8%E3%81%84%E3%81%9F%E6%93%AC%E4%BC%BC%E3%83%86%E3%82%99%E3%83%BC%E3%82%BF%E4%BD%9C%E6%88%90%20b766baca56034008ba830b4879d3a0e2/%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88_2021-12-09_22.23.14.png)

## Composerインストール

brew install composer

![Untitled](1%20Faker%E3%82%92%E7%94%A8%E3%81%84%E3%81%9F%E6%93%AC%E4%BC%BC%E3%83%86%E3%82%99%E3%83%BC%E3%82%BF%E4%BD%9C%E6%88%90%20b766baca56034008ba830b4879d3a0e2/Untitled.png)

## Fakerのインストール

プログラム用のディレクトリを作成し以下のコマンドを実行します

composer require fzaninotto/faker

![スクリーンショット 2021-12-09 22.26.19.png](1%20Faker%E3%82%92%E7%94%A8%E3%81%84%E3%81%9F%E6%93%AC%E4%BC%BC%E3%83%86%E3%82%99%E3%83%BC%E3%82%BF%E4%BD%9C%E6%88%90%20b766baca56034008ba830b4879d3a0e2/%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88_2021-12-09_22.26.19.png)

## PHPプログラム

composerで作成されたvendorディレクトリと同じ階層に以下のPHPファイルを作成します

```php
<?php
//autoloadでfakerインポート
require_once 'vendor/autoload.php';

//fakerを日本語に設定
$faker = Faker\Factory::create('ja_JP');

$data = [];
//for文で10人分データ用意
for ($i = 0; $i < 10; $i++) {
	$data[] = [
		'name'     => $faker->name,            // 名前
		//'zip'      => $faker->postcode,        // 郵便番号
		//'pref'     => $faker->prefecture,      // 都道府県
		//'city'     => $faker->city,            // 市
		'address'  => $faker->streetAddress,   // 住所
		'phone'    => $faker->phoneNumber,     // 電話番号
		'email'    => $faker->safeEmail,       // メール
		//'birthday' => $faker->dateTimeBetween('-80 years', '-20years')
		//					->format('Y-m-d'), // 生年月日 (20〜80年前の日付)
		//'score'    => $faker->numberBetween(1, 100), // 1〜100の数字
		'text'     => $faker->realText(20),    // ダミーテキスト 20文字
	];
}
//連装配列をjson形式にエンコードして出力
echo json_encode($data, JSON_UNESCAPED_UNICODE | JSON_PRETTY_PRINT), PHP_EOL;

?>
```

## PHP実行

先に作成したPHPファイルを実行します

```bash
php faker_echo.php
```

![スクリーンショット 2021-12-09 23.17.14.png](1%20Faker%E3%82%92%E7%94%A8%E3%81%84%E3%81%9F%E6%93%AC%E4%BC%BC%E3%83%86%E3%82%99%E3%83%BC%E3%82%BF%E4%BD%9C%E6%88%90%20b766baca56034008ba830b4879d3a0e2/%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88_2021-12-09_23.17.14.png)

ランダムな名前、住所、電話番号、Eメール、テキストが出力されれば成功です！