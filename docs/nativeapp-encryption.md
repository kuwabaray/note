# ネイティブアプリでの暗号化
## 認証トークン
モバイルアプリではログインをした後アプリをキルしてもログイン状態が保持され、
一定期間は再ログイン不要のサービスが多いです。

HTTPはステートレス（各リクエスト間で認証状態等を引き継がない）ため、
Authorization Bearerヘッダに認証トークンを毎回保持させます。

X


### 認証アーキテクチャ
認証サーバー・DB
1. ログインAPIが叩かれたとき認証トークンをランダムに生成する（推測されないため）。
2. 認証トークンをDBに保存。
3. 暗号化された通信によるAPIリクエストに対して、認証トークンがDBにあり有効期限が切れていないことチェック。
4. 有効期限が切れていた場合は認証トークンをDBから削除
5. サービスによっては有効期限の延長を行う。

クライアント（今回の場合モバイルアプリ）
1. ログイン成功時に認証トークンを受け取る。
2. メモリに持つまたは、機密情報であるため安全にローカルストレージに保管する。
   （メモリに持つ場合、アプリをキルするとトークンを失う。）
4. APIリクエスト時に使用する。
認証トークンが有効期限切れだった場合はログアウトする。
### リフレッシュトークン
サービスによっては認証トークンとして「アクセストークン」と「リフレッシュトークン」を持つ場合がある。

```
アクセストークン
・先述した認証トークンのこと。
・APIリクエストで何回も使用されるが、有効期限が短いため盗難された場合のリスクが低い。

リフレッシュトークン
・アクセストークンの有効期限が切れたときにリフレッシュトークンを用いて認証サーバーにアクセストークンの再発行をリクエストする。
・アクセストークンと同様ローカルストレージに保存する。
・有効期限が長い。
・重要度がアクセストークンよりも高いが、APIリクエストに使用する頻度が低いためリスクも低い。
```




