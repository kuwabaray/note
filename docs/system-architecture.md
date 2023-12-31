# システム設計
## エラーハンドリング
### 業務エラーとは
ユーザーがどうにかできる(関係ある)エラー。
```
例） ・リクエスト値が想定外
　 　・リクエストを送っていいステータスではない
```
→ ユーザーの画面に次のアクションを表示する。
### システムエラーとは
ユーザーがどうにもできない(関係ない)エラー。
```
例） ・別システムからのレスポンスが想定外
　 　・DBにマスタデータがない
　 　・権限違反
```

→ 関数内では必ず細かい引数バリデーションを行う。<br>
→ 不備がある場合エラー(基本的に業務エラー)を投げる。

### エラーハンドリング
**エラーは基本キャッチしない。**<br>
キャッチする場合<br>
1. ログの情報を加えたい場合
2. その処理だけスキップして、次の処理に行かせたい場合<br>
 → 2.1. バッチでforループで複数アカウントについて処理している。あるアカウントについてエラーがあったらスキップして次のアカウントの処理をしたい。<br>
 → 2.2. DBに保存している情報を画面にそのまま返す系のAPIで、あるDBの情報がnullであればif (dbData1 != null) で回避して他の情報取得処理に進む。<br>


#### DBデータのnull処理
- なるべくDBのカラムはNot Nullにする。
```
例） 年齢から借入最大額を計算するのにDBに保存されている年齢が必要なとき、
　 　notNullであれば、nullチェックなくアプリケーション側で計算に使用できる。
```
- Nullableの場合、application側でnullチェック、nullのとき業務エラー発砲する。<br>
  （年齢は保存されている前提の場合、ユーザーに年齢の保存を促す）
- 前述の通り、参照して表示ぐらいならNullableでいい。

### なるべくNullチェックをしないテクニック
- 自作関数でnullを返さない。リストを返す関数ならからのリストを返す。
- 返り値、パラメーターにプリミティブ型を使用する。Integerではなくint。
- 文字列を比較するときは、定数にequals()メソッドを利用する。<br>
  (* javaのLocalDate.equals()だと引数にnullでもエラーが起こるので注意)
```
例）
boolean isServiceUnavailable(response) {
	String SERVICE_UNAVAILABLE = "503";
	return SERVICE_UNAVAILABLE.equals(response.getStatusCode());
}
```
