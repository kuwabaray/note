# Android OS
## アプリインストールから起動
AndroidはLinuxベースのOSであり、各アプリをLinuxのユーザーと見立てて機能している。
1. **空いているuidをインストールするアプリに振る。**

   - 10000 ~ 19999で空いている値を使う。そのため、アプリを再インストールしたときに異なるuidなこともある。
   - 他のアプリのデータにアクセスしたいときはAndroidManifest.xmlのSharedUserIdを利用すると複数のアプリに同じuidを振れたが、
   APIレベル29で非推奨となった。
   
2. **installdというプロセスが/data/data/com.company.sample-app/ というディレクトリを作成し、1のuidにchownする。**

   - 基本的にインストールの処理はroot権限を持たないPackageManagerServiceが行うが、
   root権限が必要なこの処理はroot権限を持つinstalldが行う。
   - root権限を持つプロセスの作業を切り出して少なくすることでセキュリティを高くしている。
   
3. **/data/system/package.xmlにuidなどを書き込む**

   以下の項目が全てのアプリについて記載されている。
   - アプリケーションID (com.company.sample-app)。build.gradleのapplicationIdで定義される。
   - apkファイルのパス
   - ネイティブライブラリのパス
   - uid
   - アプリ署名鍵 (参考資料[2]参照)
   - AndroidManifest.xmlに記載された使用するパーミッション
     
4. **アプリをタップされたら、package.xmlに記載されているuidでプロセスを立ち上げる。**
   
   - アプリごとにプロセスが分かれていることで、他のアプリがクラッシュしても自アプリはクラッシュしない。
   - （root権限がなければ）他プロセスの情報にアクセスできないので安全。
     
5. **アプリの使用に伴い、/data/data/com.company.sample-app/下にディレクトリが生成される。**
   
   - cache, shared_prefs, databaseは権限771なので他アプリから読み取り等されない。
  
## メモリ管理
デスクトップPCなどではメモリをディスクに退避させる（スワップ）が、Androidでは行わない。＊1 <br>
AndroidではLow Memory Killer がバックグラウンドに行って時間の経つアプリをキルしてメモリを確保する。
```
＊１ AndroidにおけるディスクはmicroSDカードなどのフラッシュメモリであり、
フラッシュメモリは処理が遅い、バッテリー消費が激しい、回数制限があるというデメリットがあるため、
メディアの保存に向いているがメモリの退避には向かないためである。
```

## 参考資料
[1]. 有野 和馬 「Androidを支える技術 II 真のマルチタスクに挑んだモバイルOSの心臓部」 <br>
[2]. https://developer.android.com/studio/publish/app-signing?hl=ja

