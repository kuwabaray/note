# Webブラウザ
## HTTPS
HTTPで送受信されるデータは暗号化されていないため、通信路で盗聴・改ざんされる恐れがある。
HTTPSはSSL/TLSプロトコルによって安全に送受信される。

### SSL/TLS
SSLの後継がTLSで総じてSSL/TLSと呼ばれることが多い。

SSL/TLSでは共通鍵・暗号鍵のハイブリット方式で暗号化される。
#### アルゴリズム
* ハンドシェイク
  - 接続要求
  - サーバー側が秘密鍵・公開鍵を生成。
  - クライアント側に公開鍵を送信。
  - クライアント側は公開鍵でプリマスターシークレットという乱数を生成し、公開鍵で暗号化する。これが共通鍵となる。
  - サーバー側にプリマスターシークレットを送信。
  - サーバー側は秘密鍵で復号。
  - サーバー側、クライアント側はそれぞれプリマスターシークレットから共通鍵を生成。
* 通信
  - 共通鍵で送受信するデータを暗号化・復号化する。

## Cookie
Cookieは「key=value」のデータ型をしている。<br>
HTTPはステートレスなので、リクエスト間で認証情報やトラッキング情報を保持するために使われる。
```
Set-Cookie: sessionId=38afes7a8; Expired=2023/01/01 00:00:00
```

### Cookieを保護するためのオプション
1. Secure<br>
Secureの付与されたCookieはHTTP通信ではセットされず、HTTPS通信でのみセットされる。
```
Set-Cookie: sessionId=38afes7a8; Secure
```

2. HttpOnly<br>
HttpOnlyを設定することで、javascriptからDocument.cookieなどを使用してCookieを読み取ることができなくなる。
Cookieの読み取りは認証サーバー側で行えばいいのでjavascriptを使用する必要はない。
これによってXSS脆弱性の対策となる。
```
Set-Cookie: sessionId=38afes7a8; HttpOnly
```

3. SameSite<br>
例えば、他人のCookieを利用して罠ページから購入完了ページに遷移されるリクエストを送った場合、正規のページ遷移をしていない
にも関わらず購入完了してしまう。これをCSRF攻撃という。
SameSiteを設定することで、遷移元と遷移先のサイトが一致する場合にのみCookieがセットされるようになる。
```
Set-Cookie: sessionId=38afes7a8; SameSite=Strict
```



