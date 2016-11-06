# node.jsでWebSocket(解説)

## 使い方

ターミナルで以下にアクセス

```
node app.js
```

ブラウザで以下にアクセス

```
http://localhost:3000/
```

# コードの解説

## Socket.IO基本ライブラリー図

![socket.IO基本ライブラリー図](./socket.IO基本ライブラリー図.png)

## socket.ioの読み込みについて

```
<script type="text/javascript" src="/socket.io/socket.io.js"></script>
```

Socket.ioのクライアントライブラリーを読み込む

実際のJSファイルを読み込む必要なし。サーバー起動時に、"/socket.io/socket.io.js"ライブラリを自動生成する

## サーバー側の準備

```
//必要なモジュールの読み込み
var http = require('http');
var socketio = require('socket.io');
var fs = require('fs');

//HTTPサーバーの生成
var server = http.createServer(function(req, res) {
    res.writeHead(200, {'Content-Type' : 'text/html'});
    res.end(fs.readFileSync(__dirname + '/index.html', 'utf-8'));
}).listen(3000);  // ポート競合の場合は値を変更


//HTTPサーバーにソケットを紐付ける
var io = socketio.listen(server);
```

### クライアント側の準備

```
var socket = io.connect();
```


## 接続と切断

### 接続

```
socket.on('connect', function() {
    // この中に接続時の処理を記述する
});
```

### 切断

```
socket.on('disconnect', function() {
    // この中に切断時の処理を記述する
});
```

## 送受信の基本メソッド

|データの送信|データの受信|
|:-----|:-----|
|emit(送信イベント名,送信データ)関数|on(受信イベント名,受信データ)関数

#### 送信の例

```
io.sockets.emit('server_to_client', {value : data.value});
```

#### 受信の例

```
socket.on('client_to_server', function(data) {
    io.sockets.emit('server_to_client', {value : data.value});
});
```

##ブロードキャスト送信

送信したクライアント以外の全てのクライアントにデータを送信する

```
socket.broadcast.emit(送信イベント名, 送信データ)
```

#### サーバー側の処理の例

```
socket.on('client_to_server_broadcast', function(data) {
    socket.broadcast.emit('server_to_client', {value : data.value});
});
```

#### クライアント側の処理の例
```
socket.emit("client_to_server_broadcast", {value : entryMessage});
```

## 個別送信

Socket.IOでアクセスしたクライアントは必ず一意のIDが割り当てられる。そのIDを利用して、特定のクライアントに対してサーバーからデータを送信することができる

```
io.to(ID).emit(送信イベント名, 送信データ)
```

#### サーバー側の処理の例

```
socket.on('client_to_server_personal', function(data) {
    var id = socket.id;
    name = data.value;
    var personalMessage = "あなたは、" + name + "さんとして入室しました。"
    io.to(id).emit('server_to_client', {value : personalMessage})
});
```

#### クライアント側の例

```
socket.emit("client_to_server_personal", {value : name});
```

## ROOMを使った処理

roomを使用するとその部屋に属しているクライアント間のみデータのやりとりができる。スレッドや参加者によって、部屋を分けたい場合に使用する

#### ROOMへの入室
```
socket.join(room名)
```

#### ROOMからの退室
```
socket.leave(room名)
```

####全クライアントの送信処理は以下のようになる

|変更前|変更後|
|:-----|:-----|
|io.sockets.emit（全クライアント送信）|io.to(room).emit|
|socket.broadcast.emit（ブロードキャスト送信|socket.broadcast.to(room).emit|

#### サーバー側の例
```
socket.on('client_to_server', function(data) {
    io.to(room).emit('server_to_client', {value : data.value});
});
```

####クライアント側の例
```
socket.emit("client_to_server_join", {value : selectRoom});
```



