# 目次
+ [TCP/IP](docs/tcpip.md)
  - [IP](docs/tcpip.md#ip)
  - [TCP](docs/tcpip.md#tcp)
  - [UDP](docs/tcpip.md#udp)
  - [HTTP](docs/tcpip.md#http)
  - [Socket.io Library](docs/tcpip.md#websocket)
    * [HTTP long-polling](docs/tcpip.md#http-long-polling)
    * [WebSocket](docs/tcpip.md#websocket)
  - [HTTPS](docs/tcpip.md#https)
  - [SSL/TLS](docs/tcpip.md#ssltls)
  - [CDN, LB](docs/tcpip.md#cdn-lb)
    * [CDN, LBなど前段サーバーの役割](docs/tcpip.md#cdn%E3%83%AA%E3%83%90%E3%83%BC%E3%82%B9%E3%83%97%E3%83%AD%E3%82%AD%E3%82%B7%E3%82%B5%E3%83%BC%E3%83%90%E3%83%BC%E3%81%AE%E4%B8%BB%E3%81%AA%E5%BD%B9%E5%89%B2)
    * [CDN (Content Delivery Network)](docs/tcpip.md#cdn-content-delivery-network)
    * [LB (Load Balancer)](docs/tcpip.md#lb-load-balancer)
    * [NGINX](docs/tcpip.md#nginx)
  
+ [Webブラウザのセキュリティ](docs/web-browser-security.md)
  - [Cookie](docs/web-browser-security.md#cookie)
  - [Cookieを保護するためのオプションと代表的な脆弱性](docs/web-browser-security.md#cookie%E3%82%92%E4%BF%9D%E8%AD%B7%E3%81%99%E3%82%8B%E3%81%9F%E3%82%81%E3%81%AE%E3%82%AA%E3%83%97%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%A8%E4%BB%A3%E8%A1%A8%E7%9A%84%E3%81%AA%E8%84%86%E5%BC%B1%E6%80%A7)
    * [1. Secure](docs/web-browser-security.md#1-secure)
    * [2. HttpOnly （XSS脆弱性）](docs/web-browser-security.md#2-httponly-xss%E8%84%86%E5%BC%B1%E6%80%A7)
    * [3. SameSite （CSRF脆弱性）](docs/web-browser-security.md#3-samesite-csrf%E8%84%86%E5%BC%B1%E6%80%A7)
    * [4. Expires, Max-Age](docs/web-browser-security.md#4-expires-max-age)
    * [5. Domain](docs/web-browser-security.md#5-domain)
    * [6. Path](docs/web-browser-security.md#6-path)
  - [CORS (Cross Origin Resource Sharing)](docs/web-browser-security.md#cspcontents-security-policy)
  - [CSP　(Contents Security Policy)](docs/web-browser-security.md#cspcontents-security-policy)

+ [Android OS](docs/android-os.md#android-os)
  - [アプリインストールから起動](docs/android-os.md#android-os)
  - [メモリ管理](docs/android-os.md#%E3%83%A1%E3%83%A2%E3%83%AA%E7%AE%A1%E7%90%86)

+ [モバイルアプリにおける暗号化](docs/nativeapp-encryption.md)
  - [認証トークン](docs/nativeapp-encryption.md)
  - [認証アーキテクチャ](docs/nativeapp-encryption.md#%E8%AA%8D%E8%A8%BC%E3%82%A2%E3%83%BC%E3%82%AD%E3%83%86%E3%82%AF%E3%83%81%E3%83%A3)
  - [リフレッシュトークン](docs/nativeapp-encryption.md#%E3%83%AA%E3%83%95%E3%83%AC%E3%83%83%E3%82%B7%E3%83%A5%E3%83%88%E3%83%BC%E3%82%AF%E3%83%B3)
  - [トークンのローカルでの保管方法](docs/nativeapp-encryption.md#%E3%83%88%E3%83%BC%E3%82%AF%E3%83%B3%E3%81%AE%E3%83%AD%E3%83%BC%E3%82%AB%E3%83%AB%E3%81%A7%E3%81%AE%E4%BF%9D%E7%AE%A1%E6%96%B9%E6%B3%95)

+ [設計](docs/system-architecture.md)
  - [エラーハンドリング](docs/system-architecture.md#%E3%82%A8%E3%83%A9%E3%83%BC%E3%83%8F%E3%83%B3%E3%83%89%E3%83%AA%E3%83%B3%E3%82%B0)https://github.com/kuwabaray/note/blob/main/docs/system-architecture.md#%E3%82%A8%E3%83%A9%E3%83%BC%E3%83%8F%E3%83%B3%E3%83%89%E3%83%AA%E3%83%B3%E3%82%B0)
  - [業務エラーとは](docs/system-architecture.md#%E6%A5%AD%E5%8B%99%E3%82%A8%E3%83%A9%E3%83%BC%E3%81%A8%E3%81%AF)
  - [システムエラーとは](docs/system-architecture.md#%E3%82%B7%E3%82%B9%E3%83%86%E3%83%A0%E3%82%A8%E3%83%A9%E3%83%BC%E3%81%A8%E3%81%AF)
  - [エラーハンドリング](docs/system-architecture.md#%E3%82%A8%E3%83%A9%E3%83%BC%E3%83%8F%E3%83%B3%E3%83%89%E3%83%AA%E3%83%B3%E3%82%B0-1)
  - [なるべくNullチェックをしないテクニック](docs/system-architecture.md#%E3%81%AA%E3%82%8B%E3%81%B9%E3%81%8Fnull%E3%83%81%E3%82%A7%E3%83%83%E3%82%AF%E3%82%92%E3%81%97%E3%81%AA%E3%81%84%E3%83%86%E3%82%AF%E3%83%8B%E3%83%83%E3%82%AF)
