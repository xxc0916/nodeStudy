# First Chapter

var express = require\('express'\)

    , app = express\(\)

    , server = require\('http'\).createServer\(app\)

    , io = require\('socket.io'\).listen\(server\);

server.listen\(3001\);

app.get\('/', function \(req, res\) {

    res.sendfile\(\_\_dirname + '/index.html'\);}\);

io.sockets.on\('connection', function \(socket\) {

    socket.emit\('news', { hello: 'world' }\);

    socket.on\('other event', function \(data\) {

        console.log\(data\);

    }\);

}\);

&lt;script src="/socket.io/socket.io.js"&gt;&lt;/script&gt;

&lt;script&gt;

    var socket = io.connect\('http://localhost'\);

    socket.on\('news', function \(data\) {

        console.log\(data\);

        socket.emit\('other event', { my: 'data' }\);

    }\);

&lt;/script&gt;







1\).向所有客户端广播：socket.broadcast.emit\('broadcast message'\);

2\).进入一个房间（非常好用！相当于一个命名空间，可以对一个特定的房间广播而不影响在其他房间或不在房间的客户端）：socket.join\('your room name'\);

3\).向一个房间广播消息（发送者收不到消息）：socket.broadcast.to\('your room name'\).emit\('broadcast room message'\);

4\).向一个房间广播消息（包括发送者都能收到消息）（这个API属于io.sockets）：io.sockets.in\('another room name'\).emit\('broadcast room message'\);

5\).强制使用WebSocket通信：（客户端）socket.send\('hi'\)，（服务器）用socket.on\('message', function\(data\){}\)来接收。

//1. 向my room广播一个事件，提交者会被排除在外（即不会收到消息）

io.sockets.on\('connection', function \(socket\) {

    //注意：和下面对比，这里是从客户端的角度来提交事件

    socket.broadcast.to\('my room'\).emit\('event\_name', data\);

}

//2. 向another room广播一个事件，在此房间所有客户端都会收到消息

//注意：和上面对比，这里是从服务器的角度来提交事件

io.sockets.in\('another room'\).emit\('event\_name', data\);

//向所有客户端广播

io.sockets.emit\('event\_name', data\);

//获取所有房间的信息

//key为房间名，value为房间名对应的socket ID数组

io.sockets.manager.rooms

//获取particular room中的客户端，返回所有在此房间的socket实例

io.sockets.clients\('particular room'\)

//通过socket.id来获取此socket进入的房间信息

io.sockets.manager.roomClients\[socket.id\]



socket.join\(e\);//将链接放入e房间

io.to\(e\).emit\('msg',socket.id\)//对e房间理的链接发送消息，socket.id为唯一链接的名字





客户端socket.on\(\)监听的事件：



connect：连接成功

connecting：正在连接

disconnect：断开连接

connect\_failed：连接失败

error：错误发生，并且无法被其他事件类型所处理

message：同服务器端message事件

anything：同服务器端anything事件

reconnect\_failed：重连失败

reconnect：成功重连

reconnecting：正在重连

当第一次连接时，事件触发顺序为：connecting-&gt;connect；当失去连接时，事件触发顺序为：disconnect-&gt;reconnecting（可能进行多次）-&gt;connecting-&gt;reconnect-&gt;connect。

设置命名空间

有的时候要一个程序支持多个应用，如果使用默认的 “/” 命名空间可能会比较混乱。如果想让一个连接可以支持多个连接，可以使用如下的命名空间的方法：



app.js

var io = require\('socket.io'\).listen\(80\);



var chat = io

 .of\('/chat'\)

 .on\('connection', function \(socket\) {

   socket.emit\('a message', {

       that: 'only'

     , '/chat': 'will get'

   }\);

   chat.emit\('a message', {

       everyone: 'in'

     , '/chat': 'will get'

   }\);

 }\);

