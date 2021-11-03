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
