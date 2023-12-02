# Android OSの仕組み
## 
AndroidはLinuxベースのOSであり、各アプリをLinuxのユーザーと見立てて機能している。
1. **空いているuidをインストールするアプリに振る。**

   - 10000 ~ 19999で空いている値を使う。そのため、アプリを再インストールしたときに異なるuidなこともある。
   - 他のアプリのデータにアクセスしたいときはAndroidManifest.xmlのSharedUserIdを利用すると複数のアプリに同じuidを振れたが、
   APIレベル29で非推奨となった。
   
2. **installdというプロセスが/data/data/com.company.sample-app/ というディレクトリを作成し、1のuidにchownする。**

   - 基本的にインストールの処理はroot権限を持たないPackageManagerServiceが行うが、
   root権限が必要なこの処理はroot権限を持つinstalldが行う。
   - root権限を持つプロセスの作業を切り出して少なくすることでセキュリティを高くしている。
   
3. **package.xmlにuidとパッケージ名 (com.company.sample-app)、apkファイルが置かれている箇所などを書き込む**
4. **アプリをタップされたら、package.xmlに記載されているuidでプロセスを立ち上げる。**
5. **アプリの使用に伴い、/data/data/com.company.sample-app/下にディレクトリが生成される。**
   - cache, shared_prefs, databaseは権限771なので他アプリから読み取り等されない。
   

