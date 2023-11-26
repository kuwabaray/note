# Webブラウザ
## HTTPS
HTTPで送受信されるデータは暗号化されていないため、通信路で盗聴・改ざんされる恐れがある。
HTTPSはSSL/TLSプロトコルによって安全に送受信される。

## SSL/TLS
SSLの後継がTLSで総じてSSL/TLSと呼ばれることが多い。

SSL/TLSでは共通鍵・暗号鍵のハイブリット方式で暗号化される。
### アルゴリズム
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

## Cookieを保護するためのオプションと代表的な脆弱性
### 1. Secure<br>

Secureの付与されたCookieはHTTP通信ではセットされず、HTTPS通信でのみセットされる。
```
Set-Cookie: sessionId=38afes7a8; Secure
```

### 2. HttpOnly （XSS対策）<br>

HttpOnlyを設定することで、javascriptからDocument.cookieなどを使用してCookieを読み取ることができなくなる。
Cookieの読み取りは認証サーバー側で行えばいいのでjavascriptを使用する必要はない。
```
Set-Cookie: sessionId=38afes7a8; HttpOnly
```
これによってXSS脆弱性の対策となる。
#### XSS脆弱性
メモを保存、表示する機能があるとする。<br>
```
<script>something()<script>
```
のようなメモを表示したとき、javascriptのコードsomething()が実行されてしまう。

### 3. SameSite （CSRF対策）<br>
#### CSRF脆弱性
Cookieは同じ宛先へのリクエストには自動で付与される。
そのため以下のようなCSRF攻撃を受けることある。
```
銀行ページ(http://xxx.xxx)にログインしていたとする。
罠ページ(http://yyy.yyy)へのURLが書かれたメールが届く。
罠ページへアクセスしたところ振込リクエスト(http://xxx.xxx/buy)が自動送信されてしまう。
別サイトからのリクエストだが宛先が同じなので振込リクエストにCookieが付与され、セッションIDが有効であるため処理が完了してしまう。
```
これはSameSiteを設定することが大きな対策となる。SameSiteはクライアントからサーバーへのリクエストについて制限するプロパティである。
SameSiteをStrictで設定すると、リクエスト元とリクエスト先のスキーム+ホスト名(http:// + xxx.xxx)が一致する場合にのみCookieがセットされるようになる。
デメリットとして、悪意なく別サイトのリンクから銀行ページに戻るとセッションが切れてしまう。
```
Set-Cookie: sessionId=38afes7a8; SameSite=Strict
```

### 4. Expires, Max-Age

ExpiredはCookieの有効期限を日付で指定し、Max-Ageは秒数で指定する。両方とも指定された場合はMax-Ageが優先される。
```
Set-Cookie: sessionId=38afes7a8; Expires: Wed, 21 Oct 2015 07:28:00 GMT; Max-Age: 3600
```

### 5. Domain
DomainはCookieが利用されるホストを制限する。しかしサブドメインまでCookieの使用を許容してしまう。
指定しない場合サブドメインには使用されないため指定しない方がセキュリティ面で良い。
>メインサイトとは別のコンテンツを作るときに、本体ドメインを元に任意で設定するドメイン名のことです。 「○○○.com」（独自ドメイン）を本体ドメインとしたとき、「△△△. ○○○.com」のように本体ドメインの先頭に文字列を挿入しているドメインがサブドメインになります。 (https://shop-pro.jp/yomyom-colorme/72260)
```
Set-Cookie: sessionId=38afes7a8; Domain:example.com
```
### 6. Path

PathはCookieが利用されるホストのパスを制限する。Cookieが奪われる状況についてPathの制限によってセキュリティが改善することはないので指定不要。
