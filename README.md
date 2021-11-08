## \$ npm install -g json-server

- \$ json-server --watch db.json<br>

## LICENSE

The source code is licensed MIT.

## Summary

This is the sample repository to test the request and the response of a simple json api server.

## Methods

### GET

`curl -X GET "http://localhost:3000/episodes" -v`

### POST

`curl -X POST -H "Content-Type: application/json" -d '{"id": 4, "title": "ロズワール邸の団欒"}' "http://localhost:3000/episodes" -v`

### PUT

`curl -X PUT -H "Content-Type: application/json" -d '{"id": 4, "title": "ロズワール邸の争乱"}' "http://localhost:3000/episodes/4" -v`

### DELETE

`curl -X DELETE "http://localhost:3000/episodes/4" -v`

### HEAD

`curl -X HEAD "http://localhost:3000/episodes" -v`

### OPTIONS

`curl -X OPTIONS "http://localhost:3000/episodes" -v`

## ステータスコードの大分類

|ステータスコード|意味|詳細|
|---|---|---|
|1xx|処理中|処理が継続していることを示す。クライアントがそのままリクエストを継続するかなど判定|
|2xx|成功|リクエストの成功を示す|
|3xx|リダイレクト|他のソースへのレイダイレクトを示す。Locationヘッダーを見て新しいリソースへ接続する。|
|4xx|クライアントエラー|クライアントのリクエストが原因でエラーが発生したことを示す|
|5xx|サーバーエラー|サーバー側で何らかのエラーが発生したことを示す|

+ 先頭の数字でどのようなレスポンスか把握<br>
+ 未知のステータスコードは先頭の数字で対応<br>

## よく使うステータスコード

|ステータスコード|テキストフレーズ|意味・特徴|
|---|---|---|
|200|OK|GET: 取得したリソースがメッセージ本文で送信される HEAD: ヘッダー情報がメッセージ本文で送信される PUT or POST: 操作の結果がメッセージ本文で送信される|
|201|Created|新たなリソースが作成された。PUTまたはPOSTのレスポンス|
|204|No Content|リクエストのレスポンスとして送信するコンテンツはないがリクエストは成功|
|301|Moved Permanently|リソースの恒久的な移動|
|400|Bad Request|リクエストの構文やパラメータが間違っている|
|403|Forbidden|認証されていないなどコンテンツへのアクセス権がない|
|404|Not Found|リソースが存在しない、URLを解釈できなかったなど|
|500|Internal Server Error|ゲートウェイとなるサーバーが正常に動作しなかった|
|503|Service Unavailable|サーバーがメンテナンスや過負荷などでダウンしている|

## ステータスコードとエラー処理

+ ステータスコードに応じてエラーレスポンスを返そう<br>

+ Acceptヘッダに応じたフォーマットでエラーを返す<br>
 type/html : HTMLでエラーを示すページを作成して返す<br>
 application/json : JSONでresponse.error.messageなどを表示する<br>

+ SPAのバックエンドとしてJSON形式のAPIを利用している時<br>
 ステータスコードに応じてコンポーネントを生成<br>
```
const fetchData = async () => {
  const res = await fetch('http://localhost:3000/episodes)
  if (res.status === 400) {
    // クライアントエラーの処理
  }
}
```

## MIMEメディアタイプの指定

+ MIMEはMultipurpose Internet Mail Extensionsの略<br>
+ Content-Typeヘッダでメディアタイプを指定（type/subtypeの形式)<br>
+ charsetパラメータは文字エンコーディングを指定<br>

`Content-Type: application/json; charset=utf-8`<br>

|主要なType|意味|type/subtype例|
|---|---|---|
|text|人が読んで直接理解できる|text/plain|
|image|画像データ|image/jpeg|
|audio|音声データ|audio/mpeg|
|video|動画データ|video/mp4|
|Application|その他データ|application/json|

## 様々なヘッダ情報

|ヘッダ種別|例|意味|
|---|---|---|
|言語タグ|Content-Language: ja-JP|リソースを表現する自然言語|
|コンテントネゴシエーション|Accept: text/html, application/xml|クライアントが処理可能なメディアタイプ|
|ボディの長さ|Content-Length: 447|レスポンスボディのサイズ|
|チャンク転送|Transfer-Encoding: chunked|大きなデータをチャンク（分けて）転送する|

## HTTP認証のためのヘッダ

+ リソースのアクセスに制限がかかっている場合、認証が必要<br>
+ Authorizationヘッダに認証情報を付与する<br>

`Authorization: Basic asdughawudfahjh==` asdughawudfahjh==はbase64エンコードで人には読めないが、簡単にデコードできる<br>

|認証方法|仕様|特徴|
|---|---|---|
|Basic認証|base64でエンコードされたユーザーIDとパスワードを使用|簡単にデコードできる HTTPSによる通信暗号化が必須|
|Digest認証|デコードできないハッシュ化されたパスワードを使用|メッセージは暗号化されないのでHTTPSによる通信暗号化が必須|
|Barer認証|権限付きのトークンを取得して使用する|OAuth2.0で保護されたリソースなどの認証に使う|

## キャッシュ

+ リソースを再利用できる仕組み<br>
+ 現在の使用ではCache-Controlヘッダを使うことがほとんど<br>
+ 検証と再取得の条件を設定する<br>

|指定方法|目的|
|---|---|
|Cache-Control: no-store|キャッシュしない|
|Cache-Control: no-cache|キャッシュするが再検証する|
|Cache-Control: max-age=86400|相対的な有効期限を設定 単位は秒（86400秒=24時間）|
|Cache-Control: must-revalidate|必ず検証する|

## 実践編の前提

+ SNSのシンプルなAPIを設計・開発する<br>
+ RESTfulなAPIにする<br>
 `GET`<br>
 `POST`<br>
 `PUT`<br>
 `DELETE`<br>
+ 使用する技術<br>
 `バックエンド言語 : Node.js`<br>
 `Webサーバー : Express`<br>
 `データベース : sqlite3`<br>

## なぜユーザーを扱うのか？

+ 今回扱うリソースはUserのみ<br>
+ ほぼ全てのWebサービスに「人」が存在するから<br>
 `ECアプリ : 人と商品`<br>
 `SNS : 人と人、人と動画、人と画像...`<br>
+ 変更の多いリソースだから<br>

## 実践編の流れ

+ 環境構築をする<br>
+ リソース設計を使用<br>
+ 読み取り可能なAPIを開発する<br>
+ APIを実行する簡易フォームを作る<br>
+ 書き込み可能なAPIを開発する<br>
+ より高度なリソース設計について<br>

## 環境構築

### モジュールの概要

+ Express : Node.jsのWebアプリケーションフレームワーク<br>
+ sqlite3 : 軽量なRDB(個人開発向け)<br>
+ body-parser : HTMLフォームから送信された値をパース<br>
+ node-dev : ファイル編集を検知してサーバー再起動(ホットリロード)<br>

### $ npm install --save express sqlite3 body-parser (npm インストールする)

+ `npm install -g node-dev`を実行<br>

+ `package.json`を編集<br>

```
{
  "name": "learning-web-api",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "node-dev app/app.js" // 追記
  },
  "author": "takaki",
  "license": "MIT",
  "dependencies": {
    "body-parser": "^1.19.0",
    "express": "^4.17.1",
    "sqlite3": "^4.2.0"
  }
}
```

+ `app/app.js`を作成する<br>

```
const express = require('express')
const app = express()

app.get('/api/v1/hello', (req, res) => {
  res.json({ "message": "Hello, World!" })
})

const port = process.env.PORT || 3000;
app.listen(port)
console.log("Listen on port: " + port)
```

+ `$ npm start`を実行<br>

+ `$ curl -X GET http://localhost:3000/api/v1/hello`を叩いてみる<br>

```
{"message":"Hello, World!"}
```

+ `$ curl -X GET http://localhost:3000/api/v1/hello -v`を叩いてみる<br>

```
Note: Unnecessary use of -X or --request, GET is already inferred.
*   Trying ::1...
* TCP_NODELAY set
* Connected to localhost (::1) port 3000 (#0)
> GET /api/v1/hello HTTP/1.1
> Host: localhost:3000
> User-Agent: curl/7.64.1
> Accept: */*
>
< HTTP/1.1 200 OK
< X-Powered-By: Express
< Content-Type: application/json; charset=utf-8
< Content-Length: 27
< ETag: W/"1b-9u68i8s9Wl4qBrhqdi8s7jmwKho"
< Date: Fri, 05 Nov 2021 05:55:15 GMT
< Connection: keep-alive
< Keep-Alive: timeout=5
<
* Connection #0 to host localhost left intact
{"message":"Hello, World!"}* Closing connection 0
```

## リソースの設計とDB設計

### リソース指向アーキテクチャ

1. Webサービスで利用するデータを特定する<br>
2. データをリソースに分ける -> リソース設計<br>
3. リソースにURIで名前をつける -> URI設計<br>
4. リソースの表現を設計する -> 今回は全てJSONで<br>
5. リンクとフォームでリソースを結びつける<br>
6. イベントで標準的なコースを設計する<br>
7. エラーを想定する<br>

### SNSのデータ特定とリソース設計

+ `ユーザー情報`<br>
  ユーザーID、ユーザー名、プロフィール、写真、誕生日...etc<br>

+ `フォロー情報`<br>
  フォロワーID、フォローID<br>

`今回対象とするリソース`<br>

 1. ユーザーリソース<br>
 2. 検索結果リソース<br>

### URI設計

+ /users : ユーザーリソースのURI<br>
+ /search?q= : 検索結果リソースのURI (?qはクエリパラメータ)<br>

|メソッド|URI|詳細|
|---|---|---|
|GET|/api/v1/users|ユーザーリストの取得|
|GET|/api/v1/users/123|ユーザー情報の取得|
|POST|/api/v1/users|新規ユーザーの作成|
|PUT|/api/v1/users/123|ユーザー情報の更新|
|DELETE|/api/v1/users/123|ユーザーの削除|
|GET|/api/v1/search?q=torahack|ユーザー検索結果の取得|

`注 : POSTメソッドのURIを訂正`<br>

### データベース設計

|フィールド名|データ型|NULL許容|その他|
|---|---|---|---|
|id|INTEGER|NOT NULL|PRIMARY KEY|
|name|TEXT|NOT NULL||
|profile|TEXT|||
|date_of_birth|TEXT|||
|created_at|TEXT|NOT NULL|datetime関数で日付を取得|
|updated_at|TEXT|NOT NULL|datetime関数で日付を取得|

### CREATE TABLE

```
CREATE TABLE users (
  id INTEGER NOT NULL PRIMARY KEY,
  name TEXT NOT NULL,
  profile TEXT,
  created_at TEXT NOT NULL DEFAULT (DATETIME('now', 'loaltime')),
  updated_at TEXT NOT NULL DEFAULT (DATETIME('now', 'localtime')),
  date_of_birth TEXT
);
```

### app/dbディレクトリを作成

+ `$ sqlite3 app/db/database.sqlite3`を実行<br>
+ `usersのSQLをターミナルで実行`<br>

```
CREATE TABLE users (
  id INTEGER NOT NULL PRIMARY KEY,
  name TEXT NOT NULL,
  profile TEXT,
  created_at TEXT NOT NULL DEFAULT (DATETIME('now', 'localtime')),
  updated_at TEXT NOT NULL DEFAULT (DATETIME('now', 'localtime')),
  date_of_birth TEXT
);
```

+ `sqlite> .schema`と打つとテーブル情報が確認できる<br>
+ `sqlite> .tables`と打つとテーブル名が確認できる<br>

<h5>データ登録</h5>

+ `sqlite> INSERT INTO users (name, profile) VALUES ("Takaki", "I am the back-end engineer.");`

<h5>データ取得</h5>

+ `SELECT * FROM users;`<br>

<h5>package.jsonに追加登録</h5>

```
{
  "name": "learning-web-api",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "node-dev app/app.js",
    "connect": "sqlite3 app/db/database.sqlite3" // 追加
  },
  "author": "takaki",
  "license": "MIT",
  "dependencies": {
    "body-parser": "^1.19.0",
    "express": "^4.17.1",
    "sqlite3": "^4.2.0"
  }
}
```

 + `sqlite> `は contorl + Dで切断できる<br>

## GET用API

### GET/api/v1/usersのリソース表現

`全ユーザーのデータがオブジェクトとして配列に入って返ってくる`<br>

```
[
  {
    "id": 1,
    "name": "Subaru",
    "profile": "エミリアたんマジ天使！",
    "created_at": "2020-10-13 07:56:09",
    "updated_at": "2020-10-13 07:56:09",
    "date_of_birth: null
  },
  {
    "id": 2,
    "name": "Emilia",
    "profile": "もう、素晴のオタンコナス！",
    "created_at": "2020-10-13 07:56:09",
    "updated_at": "2020-10-13 07:56:09",
    "date_of_birth: null
  }
]
```

### GET /api/v1/users/:idのリソース表現

`指定IDにマッチしたデータが単一のオブジェクトとして返ってくる`<br>

`$ curl -X Get http://localhost:3000/api/v1/users/2`<br>

```
{
    "id": 2,
    "name": "Emilia",
    "profile": "もう、素晴のオタンコナス！",
    "created_at": "2020-10-13 07:56:09",
    "updated_at": "2020-10-13 07:56:09",
    "date_of_birth: null
  }
```

### GET /api/v1/searchのリソース表現

`検索にマッチしたデータがオブジェクトとして配列に入って返ってくる`<br>

```
[
  {
    "id": 1,
    "name": "Subaru",
    "profile": "エミリアたんマジ天使！",
    "created_at": "2020-10-13 07:56:09",
    "updated_at": "2020-10-13 07:56:09",
    "date_of_birth: null
  },
  {
    "id": 2,
    "name": "Emilia",
    "profile": "もう、素晴のオタンコナス！",
    "created_at": "2020-10-13 07:56:09",
    "updated_at": "2020-10-13 07:56:09",
    "date_of_birth: null
  }
]
```

## Node.jsのsqlite3基本メソッド

```
const dbPath = 'app/db/database.sqlite3'
const db = new sqlite3.Database(dbPath) : データベース接続開始

db.serialize(() => { // queries }) : 内部のSQLクエリを同期的に実行
db.all(sql, (err, rows)) : 全ての結果を一度に取得
db.get(sql, (err, row)) : 一つだけ結果を取得
db.run(sql, (err)) : SQLクエリを実行
db.close() : データベース接続を終了
```

<h5>一度テーブルを削除</h5>

`$ npm run connect` <br>

`sqlite> DROP TABLE users;`<br>

```
CREATE TABLE users (
  id INTEGER NOT NULL PRIMARY KEY,
  name TEXT NOT NULL,
  profile TEXT,
  created_at TEXT NOT NULL DEFAULT (DATETIME('now', 'localtime')),
  updated_at TEXT NOT NULL DEFAULT (DATETIME('now', 'localtime')),
  date_of_birth TEXT
);
```

<h5>Create sample data</h5>

```
INSERT INTO users (name, profile) VALUES ("Subaru", "エミリアたんマジ天使！");
INSERT INTO users (name, profile) VALUES ("Emilia", "もう、スバルのオタンコナス！");
INSERT INTO users (name, profile) VALUES ("Ram", "はい、スバルくんのレムです。");
INSERT INTO users (name, profile) VALUES ("Rem", "はい、スバルくんのレムです。");
INSERT INTO users (name, profile) VALUES ("Roswaal", "君は私になーぁにを望むのかな？");
```

<h5>Fetch all data from users table</h5>

`SELECT * FROM users;`で確認<br>

`control + D`で切断<br>

<h5>curl commands</h5>

Get all users: `curl -X GET http://localhost:3000/api/v1/users`<br>
Get a user by specified id: `curl -X GET http://localhost:3000/api/v1/users/3`<br>
Search users: `curl -X GET http://localhost:3000/api/v1/search`<br>

<h5>`app.js`の編集</h5>

```
const express = require('express')
const app = express()
const sqlite3 = require('sqlite3')
const dbPath = "app/db/database.sqlite3"

// Get all users
app.get('/api/v1/users', (req, res) => {
  // connect database
  const db = new sqlite3.Database(dbPath)

  db.all('SELECT * FROM users', (err, rows) => { // 結果がcallbackの引数に入ってくる
    res.json(rows)
  })

  db.close()
})

const port = process.env.PORT || 3000;
app.listen(port)
console.log("Listen on port: " + port)
```
+ `$ npm run start`で確認<br>

+ `$ curl -X GET http://localhost:3000/api/v1/users -v`を実行<br>

```
Note: Unnecessary use of -X or --request, GET is already inferred.
*   Trying ::1...
* TCP_NODELAY set
* Connected to localhost (::1) port 3000 (#0)
> GET /api/v1/users HTTP/1.1
> Host: localhost:3000
> User-Agent: curl/7.64.1
> Accept: */*
> 
< HTTP/1.1 200 OK
< X-Powered-By: Express
< Content-Type: application/json; charset=utf-8
< Content-Length: 677
< ETag: W/"2a5-3JW05LCfrf6g8b+KcQFs4hiihAM"
< Date: Sat, 06 Nov 2021 09:23:57 GMT
< Connection: keep-alive
< Keep-Alive: timeout=5
< 
* Connection #0 to host localhost left intact
[{"id":1,"name":"Subaru","profile":"エミリアたんマジ天使！","created_at":"2021-11-06 18:04:11","updated_at":"2021-11-06 18:04:11","date_of_birth":null},{"id":2,"name":"Emilia","profile":"もう、スバルのオタンコナス！","created_at":"2021-11-06 18:05:32","updated_at":"2021-11-06 18:05:32","date_of_birth":null},{"id":3,"name":"Ram","profile":"はい、スバルくんのレムです。","created_at":"2021-11-06 18:06:22","updated_at":"2021-11-06 18:06:22","date_of_birth":null},{"id":4,"name":"Roswaal","profile":"君は私になーぁにを望むのかな？","created_at":"2021-11-06 18:07:14","updated_at":"2021-11-06 18:07:14","date_of_birth":null}]* Closing connection 0
```

が返ってくる<br>

## 同じサーバーでHTMLを表示する

```
const path = require('path')
app.use(express.static(path.join(__dirname, 'public')));
```

1. path : パス指定用モジュール<br>
2. express.static() : 静的ファイルのルートディレクトリを設定<br>

`app.js`の編集<br>

```
const express = require('express')
const app = express()
const sqlite3 = require('sqlite3')
const path = require('path') // 追記

const dbPath = "app/db/database.sqlite3"

app.use(exporess.static(path.join(__dirname, 'public'))) // 追記

// Get all users
app.get('/api/v1/users', (req, res) => {
  // connect database
  const db = new sqlite3.Database(dbPath)

  db.all('SELECT * FROM users', (err, rows) => { // 結果がcallbackの引数に入ってくる
    res.json(rows)
  })

  db.close()
})

// Get a users
app.get('/api/v1/users/:id', (req, res) => {
  // connect database
  const db = new sqlite3.Database(dbPath)
  const id = req.params.id

  // バッククォーテーションで囲むことによってJavaScriptの変数を使うことができる
  db.get(`SELECT * FROM users WHERE id = ${id}`, (err, row) => { // 結果がcallbackの引数に入ってくる
    res.json(row)
  })

  db.close()
})

// Search users matching keyword
app.get('/api/v1/search', (req, res) => {
  // connect database
  const db = new sqlite3.Database(dbPath)
  const keyword = req.query.q

  db.all(`SELECT * FROM users WHERE name LIKE "%${keyword}%"`, (err, rows) => { // 結果がcallbackの引数に入ってくる
    res.json(rows)
  })

  db.close()
})

const port = process.env.PORT || 3000;
app.listen(port)
console.log("Listen on port: " + port)
```

+ `app/public/` ディレクトリを作成<br>

+ `app/public/index.html`ファイルを作成<br>

+ `app/public/js/`ディレクトリを作成<br>

+ `app/public/js/index.js`ファイルを作成<br>

+ `app/public/js/users.js`ファイルを作成<br>

+ `app/public/js/search.js`ファイルを作成<br>

### ディレクトリ構成の確認

1. app.js : Node.js実行ファイル<br>

2. database.sqlite3 : データベースファイル<br>

3. public/ : 静的ファイルのルートディレクトリ<br>

4. index.js : ページ読み込み時に関数を実行<br>

5. search.js : searchリソースのAPIを実行<br>

6. users.js : usersリソースのAPIを実行<br>

`index.html`の編集<br>

```
<!DOCTYPE html>
<html lang="ja">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Re:ゼロから始めるWeb API入門実践編</title>
    <!-- Load scripts -->
</head>

<body>
    <main>
        <h1>Re:ゼロから始めるユーザー管理</h1>
        <label for="search">ユーザー名で検索</label>
        <input type="text" id="search" />
        <button id="search-btn">検索</button>
        <table>
            <thead>
                <tr>
                    <th>ID</th>
                    <th>ユーザー名</th>
                    <th>プロフィール</th>
                    <th>誕生日</th>
                    <th>登録日時</th>
                    <th>更新日時</th>
                </tr>
            </thead>
            <tbody id="users-list">

            </tbody>
        </table>
    </main>
</body>

</html>
```

`users.js`の編集<br>

```
// 即時関数でモジュール化
const usersModule = (() => {
    const BASE_URL = "http://localhost:3000/api/v1/users";

    return {
        fetchAllUsers: async () => {
            const res = await fetch(BASE_URL);
            const users = await res.json();

            for (let i = 0; i < users.length; i++) {
                const user = users[i];
                const body = `<tr>
                                <td>${user.id}</td>
                                <td>${user.name}</td>
                                <td>${user.profile}</td>
                                <td>${user.date_of_birth}</td>
                                <td>${user.created_at}</td>
                                <td>${user.updated_at}</td>
                                </tr>`
                document.getElementById("users-list").insertAdjacentHTML('beforeend', body);
            }
        }
    }
})();
```

`index.js`の編集<br>

```
const indexModule = (() => {
    // UsersモジュールのfetchAllUsersメソッドを呼び出す
    return usersModule.fetchAllUsers();
})();
```

`index.html`の編集<br>

```
<!DOCTYPE html>
<html lang="ja">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Re:ゼロから始めるWeb API入門実践編</title>
    <!-- Load scripts -->
    <script src="js/users.js"></script> // 追記
    <script src="js/index.js"></script> // 追記
</head>

<body>
    <main>
        <h1>Re:ゼロから始めるユーザー管理</h1>
        <label for="search">ユーザー名で検索</label>
        <input type="text" id="search" />
        <button id="search-btn">検索</button>
        <table>
            <thead>
                <tr>
                    <th>ID</th>
                    <th>ユーザー名</th>
                    <th>プロフィール</th>
                    <th>誕生日</th>
                    <th>登録日時</th>
                    <th>更新日時</th>
                </tr>
            </thead>
            <tbody id="users-list">

            </tbody>
        </table>
    </main>
</body>

</html>
```

`search.js`の編集<br>

```
const searchModule = (() => {
    const BASE_URL = "http://localhost:3000/api/v1/search"

    return {
        searchUsers: async () => {
            // 検索窓への入力値を取得
            const query = document.getElementById('search').value

            const res = await fetch(BASE_URL + '?q=' + query)
            const result = await res.json()

            let body = ""

            for (let i = 0; i < result.length; i++) {
                const user = result[i]
                body += `<tr>
                            <td>${user.id}</td>
                            <td>${user.name}</td>
                            <td>${user.profile}</td>
                            <td>${user.date_of_birth}</td>
                            <td>${user.created_at}</td>
                            <td>${user.updated_at}</td>
                        </tr>`
            }

            document.getElementById('users-list').innerHTML = body
        }
    }
})()
```

`index.js`の編集<br>

```
const indexModule = (() => {
    // 検索ボタンをクリックした時のイベントリスナー 設定
    document.getElementById("search-btn")
        .addEventListener('click', () => {
            return searchModule.searchUsers();
        })
    // ここまで追記

    // UsersモジュールのfetchAllUsersメソッドを呼び出す
    return usersModule.fetchAllUsers();
})();
```

`index.html`の編集<br>

```
<!DOCTYPE html>
<html lang="ja">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Re:ゼロから始めるWeb API入門実践編</title>
    <!-- Load scripts -->
    <script src="js/search.js" defer></script> // 追記
    <script src="js/users.js" defer></script> // 修正
    <script src="js/index.js" defer></script> // 修正
</head>

<body>
    <main>
        <h1>Re:ゼロから始めるユーザー管理</h1>
        <label for="search">ユーザー名で検索</label>
        <input type="text" id="search" />
        <button id="search-btn">検索</button>
        <table>
            <thead>
                <tr>
                    <th>ID</th>
                    <th>ユーザー名</th>
                    <th>プロフィール</th>
                    <th>誕生日</th>
                    <th>登録日時</th>
                    <th>更新日時</th>
                </tr>
            </thead>
            <tbody id="users-list">

            </tbody>
        </table>
    </main>
</body>

</html>
```

## リクエストのbodyを読み取る設定

```
const bodyParser = require('body-parser');

// configer body-parser to get values from input form
app.use(bodyParser.urlencoded({ extended: true }));
app.use(bodyParser.json());
```

`app.js`の編集<br>

```const express = require('express')
const app = express()
const sqlite3 = require('sqlite3')
const path = require('path')
const bodyParser = require('body-parser') // 追記

const dbPath = "app/db/database.sqlite3"

// リクエストのbodyをパースする設定
app.use(bodyParser.urlencoded({ extended: true })) // 追記
app.use(bodyParser.json()) // 追記

// publicディレクトリを静的ファイル群のルートディレクトリとして設定
app.use(express.static(path.join(__dirname, 'public')))

// Get all users
app.get('/api/v1/users', (req, res) => {
  // connect database
  const db = new sqlite3.Database(dbPath)

  db.all('SELECT * FROM users', (err, rows) => { // 結果がcallbackの引数に入ってくる
    res.json(rows)
  })

  db.close()
})

// Get a users
app.get('/api/v1/users/:id', (req, res) => {
  // connect database
  const db = new sqlite3.Database(dbPath)
  const id = req.params.id

  // バッククォーテーションで囲むことによってJavaScriptの変数を使うことができる
  db.get(`SELECT * FROM users WHERE id = ${id}`, (err, row) => { // 結果がcallbackの引数に入ってくる
    res.json(row)
  })

  db.close()
})

// Search users matching keyword
app.get('/api/v1/search', (req, res) => {
  // connect database
  const db = new sqlite3.Database(dbPath)
  const keyword = req.query.q

  db.all(`SELECT * FROM users WHERE name LIKE "%${keyword}%"`, (err, rows) => { // 結果がcallbackの引数に入ってくる
    res.json(rows)
  })

  db.close()
})

const port = process.env.PORT || 3000;
app.listen(port)
console.log("Listen on port: " + port)
```

## Create new usersの作成

<h5>DBクエリ実行用の関数作成例</h5>

```
const run = async (sql) => {
  return new Promise((resolve, reject) => { // Promiseを返す = resolve()かreject()まで完了を待つ
    db.run(sql, (err) => {
      if (err) {
        res.status(500).send(err) // SQL実行失敗->サーバーエラー
        return reject();
      } else {
        res.json({message: "新規ユーザーを作成しました！"})
        return resolve();
      }
    })
  })
}
```

<h5>書き込み用SQLクエリの基本</h5>

```
INSERT INTO users (name, profile) VALUES ("${name}", "${profile}")

UPDATE users SET name="${name}", profile="${profile}" WHERE id="${id}"

DELETE FROM users WHERE id=${id}
```

`app.js`の編集<br>

```
const express = require('express')
const app = express()
const sqlite3 = require('sqlite3')
const path = require('path')
const bodyParser = require('body-parser')
const { resolveObjectURL } = require('buffer')

const dbPath = "app/db/database.sqlite3"

// リクエストのbodyをパースする設定
app.use(bodyParser.urlencoded({ extended: true }))
app.use(bodyParser.json())

// publicディレクトリを静的ファイル群のルートディレクトリとして設定
app.use(express.static(path.join(__dirname, 'public')))

// Get all users
app.get('/api/v1/users', (req, res) => {
  // connect database
  const db = new sqlite3.Database(dbPath)

  db.all('SELECT * FROM users', (err, rows) => { // 結果がcallbackの引数に入ってくる
    res.json(rows)
  })

  db.close()
})

// Get a users
app.get('/api/v1/users/:id', (req, res) => {
  // connect database
  const db = new sqlite3.Database(dbPath)
  const id = req.params.id

  // バッククォーテーションで囲むことによってJavaScriptの変数を使うことができる
  db.get(`SELECT * FROM users WHERE id = ${id}`, (err, row) => { // 結果がcallbackの引数に入ってくる
    res.json(row)
  })

  db.close()
})

// Search users matching keyword
app.get('/api/v1/search', (req, res) => {
  // connect database
  const db = new sqlite3.Database(dbPath)
  const keyword = req.query.q

  db.all(`SELECT * FROM users WHERE name LIKE "%${keyword}%"`, (err, rows) => { // 結果がcallbackの引数に入ってくる
    res.json(rows)
  })

  db.close()
})

// ここから Create a new user
app.post('/api/v1/users', async (req, res) => {
  // Connect database
  const db = new sqlite3.Database(dbPath);

  const name = req.body.name;
  const profile = req.body.profile ? req.body.profile : "";
  const dateOfBirth = req.body.date_of_birth ? req.body.date_of_birth : "";

  const run = async (sql) => {
    return new Promise((resolve, reject) => {
      db.run(sql, (err) => {
        if (err) {
          res.status(500).send(err);
          return reject();
        } else {
          res.json({message: "新規ユーザーを作成しました！"});
          return resolve();
        }
      })
    })
  }

  await run (`INSERT INTO users (name, profile, date_of_birth) VALUES ("${name}", "${profile}", "${dateOfBirth}")`);
  db.close();
});
// ここまで

const port = process.env.PORT || 3000;
app.listen(port)
console.log("Listen on port: " + port)
```

## PUTメソッド(更新)の作成

`app.js`の編集<br>

```
const express = require('express')
const app = express()
const sqlite3 = require('sqlite3')
const path = require('path')
const bodyParser = require('body-parser')
const { resolveObjectURL } = require('buffer')

const dbPath = "app/db/database.sqlite3"

// リクエストのbodyをパースする設定
app.use(bodyParser.urlencoded({ extended: true }))
app.use(bodyParser.json())

// publicディレクトリを静的ファイル群のルートディレクトリとして設定
app.use(express.static(path.join(__dirname, 'public')))

// Get all users
app.get('/api/v1/users', (req, res) => {
  // connect database
  const db = new sqlite3.Database(dbPath)

  db.all('SELECT * FROM users', (err, rows) => { // 結果がcallbackの引数に入ってくる
    res.json(rows)
  })

  db.close()
})

// Get a users
app.get('/api/v1/users/:id', (req, res) => {
  // connect database
  const db = new sqlite3.Database(dbPath)
  const id = req.params.id

  // 現在のユーザー情報を取得する バッククォーテーションで囲むことによってJavaScriptの変数を使うことができる
  db.get(`SELECT * FROM users WHERE id=${id}`, (err, row) => { // 結果がcallbackの引数に入ってくる
    res.json(row)
  })

  db.close()
})

// Search users matching keyword
app.get('/api/v1/search', (req, res) => {
  // connect database
  const db = new sqlite3.Database(dbPath)
  const keyword = req.query.q

  db.all(`SELECT * FROM users WHERE name LIKE "%${keyword}%"`, (err, rows) => { // 結果がcallbackの引数に入ってくる
    res.json(rows)
  })

  db.close()
})

// ここから編集
const run = async (sql, db, res, message) => {
  return new Promise((resolve, reject) => {
    db.run(sql, (err) => {
      if (err) {
        res.status(500).send(err);
        return reject();
      } else {
        res.json({ message: message });
        return resolve();
      }
    })
  })
}

// Create a new user
app.post('/api/v1/users', async (req, res) => {
  // Connect database
  const db = new sqlite3.Database(dbPath);

  const name = req.body.name;
  const profile = req.body.profile ? req.body.profile : "";
  const dateOfBirth = req.body.date_of_birth ? req.body.date_of_birth : "";

  await run(
    `INSERT INTO users (name, profile, date_of_birth) VALUES ("${name}", "${profile}", "${dateOfBirth}")`,
    db,
    res,
    "新規ユーザーを作成しました！"
  );
  db.close();
});

// Update user data
app.put('/api/v1/users/:id', async (req, res) => {
  // Connect database
  const db = new sqlite3.Database(dbPath);
  const id = req.params.id;

  // 現在のユーザー情報を取得する
  db.get(`SELECT * FROM users WHERE id=${id}`, async (err, row) => { // 結果がcallbackの引数に入ってくる
    const name = req.body.name ? req.body.name : row.name;
    const profile = req.body.profile ? req.body.profile : row.profile;
    const dateOfBirth = req.body.date_of_birth ? req.body.date_of_birth : row.date_of_birth;

    await run(
      `UPDATE users SET name="${name}", profile="${profile}", date_of_birth="${dateOfBirth}" WHERE id=${id}`,
      db,
      res,
      "ユーザー情報を更新しました！"
    );
  });

  db.close();
});
// ここまで編集

const port = process.env.PORT || 3000;
app.listen(port)
console.log("Listen on port: " + port)
```

## DELETEメソッド(ユーザー情報削除)の作成

`app.js`の編集<br>

```
const express = require('express')
const app = express()
const sqlite3 = require('sqlite3')
const path = require('path')
const bodyParser = require('body-parser')
const { resolveObjectURL } = require('buffer')

const dbPath = "app/db/database.sqlite3"

// リクエストのbodyをパースする設定
app.use(bodyParser.urlencoded({ extended: true }))
app.use(bodyParser.json())

// publicディレクトリを静的ファイル群のルートディレクトリとして設定
app.use(express.static(path.join(__dirname, 'public')))

// Get all users
app.get('/api/v1/users', (req, res) => {
  // connect database
  const db = new sqlite3.Database(dbPath)

  db.all('SELECT * FROM users', (err, rows) => { // 結果がcallbackの引数に入ってくる
    res.json(rows)
  })

  db.close()
})

// Get a users
app.get('/api/v1/users/:id', (req, res) => {
  // connect database
  const db = new sqlite3.Database(dbPath)
  const id = req.params.id

  // 現在のユーザー情報を取得する バッククォーテーションで囲むことによってJavaScriptの変数を使うことができる
  db.get(`SELECT * FROM users WHERE id=${id}`, (err, row) => { // 結果がcallbackの引数に入ってくる
    res.json(row)
  })

  db.close()
})

// Search users matching keyword
app.get('/api/v1/search', (req, res) => {
  // connect database
  const db = new sqlite3.Database(dbPath)
  const keyword = req.query.q

  db.all(`SELECT * FROM users WHERE name LIKE "%${keyword}%"`, (err, rows) => { // 結果がcallbackの引数に入ってくる
    res.json(rows)
  })

  db.close()
})

const run = async (sql, db, res, message) => {
  return new Promise((resolve, reject) => {
    db.run(sql, (err) => {
      if (err) {
        res.status(500).send(err);
        return reject();
      } else {
        res.json({ message: message });
        return resolve();
      }
    })
  })
}

// Create a new user
app.post('/api/v1/users', async (req, res) => {
  // Connect database
  const db = new sqlite3.Database(dbPath);

  const name = req.body.name;
  const profile = req.body.profile ? req.body.profile : "";
  const dateOfBirth = req.body.date_of_birth ? req.body.date_of_birth : "";

  await run(
    `INSERT INTO users (name, profile, date_of_birth) VALUES ("${name}", "${profile}", "${dateOfBirth}")`,
    db,
    res,
    "新規ユーザーを作成しました！"
  );
  db.close();
});

// Update user data
app.put('/api/v1/users/:id', async (req, res) => {
  // Connect database
  const db = new sqlite3.Database(dbPath);
  const id = req.params.id;

  // 現在のユーザー情報を取得する
  db.get(`SELECT * FROM users WHERE id=${id}`, async (err, row) => { // 結果がcallbackの引数に入ってくる
    const name = req.body.name ? req.body.name : row.name;
    const profile = req.body.profile ? req.body.profile : row.profile;
    const dateOfBirth = req.body.date_of_birth ? req.body.date_of_birth : row.date_of_birth;

    await run(
      `UPDATE users SET name="${name}", profile="${profile}", date_of_birth="${dateOfBirth}" WHERE id=${id}`,
      db,
      res,
      "ユーザー情報を更新しました！"
    );
  });

  db.close();
});

// ここから追記
// Delete user data
app.delete('/api/v1/users/:id', async (req, res) => {
  // Connect database
  const db = new sqlite3.Database(dbPath);
  const id = req.params.id;

  await run(
    `DELETE FROM users WHERE id=${id}`,
    db,
    res,
    "ユーザー情報を削除しました！"
  );

  db.close();
});
// ここまで追記

const port = process.env.PORT || 3000;
app.listen(port)
console.log("Listen on port: " + port)
```
