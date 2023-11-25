# ネイティブアプリにおける暗号化
## 認証トークン
モバイルアプリではログインをした後アプリをキルしてもログイン状態が保持され、
一定期間は再ログイン不要のサービスが多い。

HTTPはステートレス（各リクエスト間で認証状態等を引き継がない）ため、
Authorization Bearerヘッダに認証トークンを毎回保持させる。

### 認証アーキテクチャ
#### 認証サーバー・DB
1. ログインAPIが叩かれたとき認証トークンをランダムに生成する（推測されないため）。
2. 認証トークンをDBに保存。
3. 暗号化された通信によるAPIリクエストに対して、認証トークンがDBにあり有効期限が切れていないことチェック。
4. 有効期限が切れていた場合は認証トークンをDBから削除
5. サービスによっては有効期限の延長を行う。

```
* Json Web Token
JWTというユーザーIDや権限情報を暗号化してリクエストに含める方式もある。
　例)　{"sub":"1234567890","name":"John Doe","admin":true}

メリットとしてサーバー側での検証が不要のため、ユーザー数が増加したときの負荷が少ない。
```

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

KeyChainに保存すると再インストールしたときも認証情報が残っているため、
消したい場合はフラグをUserDefaultsに持たせる必要がある。

UserDefaultsでは暗号化せず保存できるが、KeyChainでは暗号化して保存される。<br>
暗号化方法は設定できない。（共通鍵、公開鍵のハイブリット式らしい）

KeyaChainはiCloudで同期できるため複数台で認証される可能性があることを考慮する必要がある。

#### android
androidにはKeyStoreという共通鍵、キーペア（暗号鍵、複合鍵情報）を格納するシステムがある。
暗号化のアルゴリズムとしてRSA(共通鍵暗号),AES（公開鍵暗号）など指定ができる。

iOSと異なり、KeyStoreはあくまで鍵を安全に保存する機能であり、
KeyStoreの鍵で暗号化したデータは通常時と同様SharedPreferencesに保存される。
そのためアプリを削除するとデータも消える。
```
・SharedPreferences
/data/data/YOUR_PACKAGE_NAME/shared_prefs/~.xml
```

デバイスによるユーザー認証（PIN、生体認証など）がされた時のみ鍵を使用できるように設定することも可能。

- 鍵の生成
```
KeyGenParameterSpec.Builder keyGenParamsBuilder = new KeyGenParameterSpec.Builder(keyName,
   // 暗号化と複合化にだけ使用できる。署名や検証などには使用できないようにして安全性を高めている。
   KeyProperties.PURPOSE_ENCRYPT | KeyProperties.PURPOSE_DECRYPT)
   // 暗号化アルゴリズムの設定
   .setBlockModes(KeyProperties.BLOCK_MODE_GCM)
   .setEncryptionPaddings(KeyProperties.ENCRYPTION_PADDING_NONE)
   // この鍵を使用する際にデバイスでのユーザー認証がされていることを必須とする。
   // ユーザー認証せず使用した場合はUserNotAuthenticatedExceptionが投げられる。
   .setUserAuthenticationRequired(true)
   // [1つ目の引数]秒以内に認証されていれば再認証をスキップできる。デフォルトは0で毎回認証が必要になる。
   // [2つ目の引数」で認証方法を設定する。下記の場合、生体認証とロック画面認証。
   .setUserAuthenticationParameters(0,
            KeyProperties.AUTH_BIOMETRIC_STRONG |
            KeyProperties.AUTH_DEVICE_CREDENTIAL);
```

- 認証ポップアップの表示<br>
  認証が必要な場合は認証ポップアップを呼び出すようプログラムする。
```
    biometricLoginButton.setOnClickListener(view -> {
            biometricPrompt.authenticate(promptInfo);
    });
```

- 認証が必要かどうかの判断<br>
例)
[cordova-plugin-secure-storage-echo](https://github.com/mibrito707/cordova-plugin-secure-storage-echo/tree/master)
では下記のように鍵を使用してエラーが起こるかで判断している。
  
```
boolean userAuthenticationRequired(String alias) {
        try {
            // Do a quick encrypt/decrypt test
            byte[] encrypted = encrypt(alias.getBytes(), alias);
            decrypt(encrypted, alias);
            return false;
        } catch (InvalidKeyException noAuthEx) {
            return true;
        } catch (Exception e) {
            // Other
            return false;
        }
    }
```

### 用語
通常、公開鍵を使って暗号化し秘密鍵を使って複合化するが、署名と検証はその逆。
- 署名 ... 秘密鍵を使った暗号化
- 検証 ... 公開鍵を使った複合化

### 疑問
- android端末上の同じ方法で保存している鍵で暗号化と複合化を行うのに、公開鍵暗号を使う必要があるのか謎

## 参考資料
https://developer.android.com/training/articles/keystore?hl=ja#UserAuthentication

https://coky-t.gitbook.io/owasp-mastg-ja/mobairuapuritesutogaido/0x04e-testing-authentication-and-session-management

https://qiita.com/sachiko-kame/items/261d42c57207e4b7002a

https://medium.com/androiddevelopers/using-biometricprompt-with-cryptoobject-how-and-why-aace500ccdb7
