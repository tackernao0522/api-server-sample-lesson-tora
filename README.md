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