# 目次
+ [TCP/IP](https://github.com/kuwabaray/note/edit/main/README.md#a-first-level-heading)

# TCP/IP

TCP/IPはprotocol familyの名前。主なprotocolは以下。
- tcp
- ip
- udp
- http

サーバーのアプリケーションがsocket APIを介してTCPまたはUDPのサービスを使用する。TCP(UDP）はOSに実装されている。<br>
TCP(UDP)のサービスはのIPを使ってパケットの送受信を行う。<br>
```
パケット ... ネットワークで送受信される情報
プロトコル ... パケットの中身（内容）, パケット送受信の手順の規定
```
## TCP
### 特徴
パケットの消失や重複があった場合に送信元に再要求する。そのためアプリケーション側でパケットの消失などを考慮する必要がなく信頼性が高い。

### サーバー側の仕組み
```
1. ソケットを開き、接続を受け付けるIPアドレス・ポート番号をソケットに対応づける。
2. クライアントからの接続を受け付ける。
3. 通信する（read(), write()）
4. ソケットを閉じる.
```

*1. ソケットを開き、接続を受け付けるIPアドレス・ポート番号をソケットに対応づける。*<br>

- IPアドレスでサーバーを特定でき、サーバーの各ポートとアプリケーションが使用しているソケットが紐づいているため、IPアドレスとポート番号で送信先を特定できる。
- クライアントがhostのIPアドレスを知るにはDNS(domain name system)を使用するのが一般的。DNSサーバーに問い合わせてURLからIPアドレスに変換する。
- ポート番号はURLから分からないので、~<br>
- javaではserverSocketクラスのインスタンスを作成して、ローカルポートとバインドすることで受け付けられる状態にする。

*2. クライアントからの接続を受け付ける。*<br>

- 新しいclient接続があるたびにaccept()によってsocketクラスのインスタンスを作成する。そのインスタンスから送信側のIPとport情報を取得できる。

*3. 通信する（read(), write()）*<br>

- 送信側でwrite, 受信側でreadで送受信される。

### クライアント側の仕組み
```
1. ソケットを開く (socket())
2. サーバに接続する (connect())
3. 通信を行なう (read(), write(), send(), recv())
4. ソケットを閉じる (close())
```

## UDP
### 特徴
server間ではなく、application間での通信目的。パケットロスを考慮してアプリケーションを実装する必要がある。

UDPの機能
 ・UDPはTCPと異なり、接続を確立する必要がなく、送信できる
 ・転送中に発生したsocketの破損等を検知し、それを破棄する
UDPのメリット
 ・接続が必要ない分、少ないデータを送受信するのに効率がいい

tcpは送受信前に接続を確立する必要があり、送受信が完了するまでその1つとしか接続で
きない。
UDPはsocket(UDPではdatagramという)を別々の宛先とやりとりできる。

UDPでもserver側はローカルポートを指定してインスタンスを作成する。これによって任>意のサーバーから受信できる。（この時TCPと違い、接続を確立する必要がない）。

socketは複数のclientから送信されてくるので、受信したsocketがどこからのものか分か
るように識別用の情報を送信する

## 疑問
- 複数applicationが一つのsocketを参照するのはどういう場合か
- HTTP IPの違い

- それぞれのprotocolは~用のような目的があり作られている。
HTTP (Hyper Text Transfer Protocol): hyperTextで記述されたデータ(html形式など)をサーバー(application server)からbrowserに送信して表示させるため
getはまさにこれだけどpost deleteとかは？

## 参照
https://www.fos.kuis.kyoto-u.ac.jp/le2soft/siryo-html/node14.html
