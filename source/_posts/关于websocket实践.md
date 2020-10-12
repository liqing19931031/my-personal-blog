---
title: 关于websocket实践
date: 2017-05-18 09:33:00
tags:
  - js
categories: 学习
---

# 关于推送服务的websocket 实践 可实现实时在线聊天
服务端代码实现

```
var WebSocketServer = require('websocket').server;
var http = require('http');

var server = http.createServer(function(request, response) {
    console.log((new Date()) + ' Received request for ' + request.url);
    response.writeHead(404);
    response.end();
});
server.listen(8080, function() {
    console.log((new Date()) + ' Server is listening on port 8080');
});

wsServer = new WebSocketServer({
    httpServer: server,
    // You should not use autoAcceptConnections for production
    // applications, as it defeats all standard cross-origin protection
    // facilities built into the protocol and the browser.  You should
    // *always* verify the connection's origin and decide whether or not
    // to accept it.
    autoAcceptConnections: false
});

function originIsAllowed(origin) {
  // put logic here to detect whether the specified origin is allowed.
  return true;
}

wsServer.on('request', function(request) {
    if (!originIsAllowed(request.origin)) {
      // Make sure we only accept requests from an allowed origin
      request.reject();
      console.log((new Date()) + ' Connection from origin ' + request.origin + ' rejected.');
      return;
    }
    
    var connection = request.accept('echo-protocol', request.origin);
    console.log((new Date()) + ' Connection accepted.');
    connection.on('message', function(message) {
      function sendMessage() {
          if (message.type === 'utf8') {
              console.log('Received Message: ' + message.utf8Data);
              connection.sendUTF('小宝大王的socket服务');
          }
          else if (message.type === 'binary') {
              console.log('Received Binary Message of ' + message.binaryData.length + ' bytes');
              connection.sendBytes(111);
          }
          setTimeout(sendMessage, 2000)
      }
      sendMessage()
    });
    connection.on('close', function(reasonCode, description) {
        console.log((new Date()) + ' Peer ' + connection.remoteAddress + ' disconnected.');
    });
});
```