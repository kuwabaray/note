# Webブラウザのセキュリティ
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

### 2. HttpOnly （XSS脆弱性）<br>

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

### 3. SameSite （CSRF脆弱性）<br>
#### CSRF脆弱性
Cookieは同じ宛先へのリクエストには自動で付与される。
そのため以下のようなCSRF攻撃を受けることある。
```
銀行ページ(https://xxx.xxx)にログインしていたとする。
罠ページ(https://yyy.yyy)へのURLが書かれたメールが届く。
罠ページへアクセスしたところ振込リクエスト(https://zzz.zzz/buy *APIのURL)が自動送信されてしまう。
別サイトからのリクエストだが宛先が同じなので振込リクエストにCookieが付与され、セッションIDが有効であるため処理が完了してしまう。
```
これはSameSiteを設定することが大きな対策となる。SameSiteはクライアントからサーバーへのリクエストについて制限するプロパティである。
SameSiteをStrictで設定すると、リクエスト元とリクエスト先のスキーム+ホスト名(http:// + xxx.xxx)が一致する場合にのみCookieがセットされるようになる。
デメリットとして、悪意なく別サイトのリンクから銀行ページに戻るとセッションが切れてしまう。
```
Set-Cookie: sessionId=38afes7a8; SameSite=Strict
```

### 4. Expires, Max-Age

ExpiredはCookieの有効期限を日付で指定し、Max-Ageは秒数で指定する。両方とも指定された場合はMax-Ageが優先される。<br>
（＊Cache-Controlのmax-ageとは異なる）
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

## CORS (Cross Origin Resource Sharing)
```
Same Origin
- スキーム + ホスト名 + ポート が一致するリソース。 (例 https:// + example.com + :80)<br>
- パスは関係ない
- ポート番号も一致する必要があるためCookieのSameSiteよりも制限が厳しい。
```

Same Originのリソース間にネットワーク越しのリクエストや、ブラウザ内でのリソース間アクセスの制限はないが、
オリジンが異なる (Cross Origin)のリソースではそれらが禁止されている。これをSOP (Same Origin Policy)という。

しかし、同じプロジェクトで別々のホスト名のサービスを連携したい場合もある。そういった場合はSOPを緩和するためにCORSを設定する。
#### HTTPリクエスト・レスポンス方法
単純リクエスト (参考資料[2]参照)では必要ないが、単純でないリクエストではCross Originに送りたいリクエストの前に、リクエストを送っていいか確認するプリフライトリクエストのやり取りをする。

##### 単純リクエストの場合
[画像]

##### 単純でないリクエストの場合
[画像]

また、CORSではデフォルトでCookieがセットされないので、Cookieを用いて認証管理をしたい場合は以下の対応が必要。
- Access-Control-Allow-Credentials: true とする。
- Access-Control-Allow-Originをワイルドカード (*) ではなく指定する

## CSP　(Contents Security Policy)
### 背景
SOPはSame Originへの制限がないため、[XSS脆弱性](https://github.com/kuwabaray/note/blob/main/docs/web-browser-security.md#xss%E8%84%86%E5%BC%B1%E6%80%A7)を代表とするコンテンツインジェクションに弱い。<br>
コンテンツインジェクションへの対策としてCSP (Contents Security Policy)がある。

### CSPとは？
CSPというHTTPヘッダでホスト名を指定することで、そのHTTPリクエストでリソースを取得した後、Webブラウザがそのホスト下のリソースを読み込まないでくれる。
例えばscript-srcにオリジンを指定すれば、オリジン下以外のjavascriptを実行されない。
```
Content-Security-Policy: default-src 'self'; script-src https://example.com;
```
default-srcが画像やスタイルシートなど全てのリソースの制限ができるディレティブである。上記例ではwebページのオリジンを指定している。
img-srcなどのディレクティブはdefault-srcの子供のイメージ。
script-srcは明示的に指定しているので、webページのオリジンではなく https://example.com 下だけが許可される。


## 参考資料
[1]. 米内貴志 「Webブラウザセキュリティ Webアプリケーションの安全性を支える仕組みを整理する」<br>
[2]. [オリジン間リソース共有 (CORS) 単純リクエスト](https://developer.mozilla.org/ja/docs/Web/HTTP/CORS#%E5%8D%98%E7%B4%94%E3%83%AA%E3%82%AF%E3%82%A8%E3%82%B9%E3%83%88)<br>
[3]. [コンテンツセキュリティポリシー(CSP)ディレクティブまとめ](https://qiita.com/yuria-n/items/c50a1bc0ba51f6e33215)<br>
