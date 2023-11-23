# ネイティブアプリでの暗号化
## 認証トークン
モバイルアプリではログインをした後アプリをキルしてもログイン状態が保持され、
一定期間は再ログイン不要のサービスが多いです。

HTTPはステートレス（各リクエスト間で認証状態等を引き継がない）ため、
Authorization Bearerヘッダに認証トークンを毎回保持させます。

### 認証アーキテクチャ
#### 認証サーバー・DB
1. ログインAPIが叩かれたとき認証トークンをランダムに生成する（推測されないため）。
2. 認証トークンをDBに保存。
3. 暗号化された通信によるAPIリクエストに対して、認証トークンがDBにあり有効期限が切れていないことチェック。
4. 有効期限が切れていた場合は認証トークンをDBから削除
5. サービスによっては有効期限の延長を行う。

#### クライアント（今回の場合モバイルアプリ）
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

### トークンのローカルでの保管方法
#### iOS
iOSにはKeyChainというパスワード等機密情報を保存するための機能がある。
一般的にローカルストレージとして使われるUserDefaultsと保存場所が異なる。
```
・UserDefaults
/data/Containers/Data/Application/APP_ID/Library/Preferences/

・KeyChain
/private/var/Keychains/keychain.db
```
そのため以下の違いが生まれる。
- KeyChainに保存したデータはアプリ削除後も消えない。UserDefaultsは消える。
- KeyChainは設定によっては他アプリとデータが共有できる。UserDefaultsはできない。

また、UserDefaultsでは暗号化せず保存できるが、KeyChainでは暗号化して保存される。
暗号化方法は設定できない。（共通鍵、公開鍵のハイブリット式らしい）

#### android
androidにはKeyStoreという共通鍵、キーペア（暗号鍵、複合鍵情報）を格納するシステムがある。

iOSと異なり、KeyStoreはあくまで鍵を安全に保存する機能であり、
KeyStoreの鍵で暗号化したデータは通常時と同様SharedPreferencesに保存される。
そのためアプリを削除するとデータも消える。
```
・SharedPreferences
/data/data/YOUR_PACKAGE_NAME/shared_prefs/~.xml
```
暗号化のアルゴリズムとしてRSA(共通鍵暗号),AES（公開鍵暗号）など指定ができる。

デバイスによるユーザー認証がされている時のみ鍵を使用するようにもできる。

1. 画面ロックが設定されていることを必須とする。<br>
KeyguardManager.isDeviceSecure()で画面ロックが設定されているか確認し、
設定されているときのみ暗号化/複合化するようにプログラムする。

2. 生体認証を必須とする。<br>
鍵生成時にオプションでsetUserAuthenticationRequired(true)とすると、
その鍵を使用してデータを暗号化/複合化しようとしたとき生体認証を求められる。

疑問：android端末上の同じ方法で保存している鍵で暗号化と複合化を行うのに、公開鍵暗号を使う必要があるのか謎

## 参考資料
https://coky-t.gitbook.io/owasp-mastg-ja/mobairuapuritesutogaido/0x04e-testing-authentication-and-session-management

https://qiita.com/sachiko-kame/items/261d42c57207e4b7002a

https://medium.com/androiddevelopers/using-biometricprompt-with-cryptoobject-how-and-why-aace500ccdb7
