---
title: SimpleWebRTC使用指南，前端实现P2P传输【视频+文件】！
date: 2024-09-29 09:09:50
categories:
  - WEB
tags:
  - 传输
  - 前端
  - RTC
  - P2P
  - 文件传输
---


腾讯云浏览器RTC支持检查网站：https://web.sdk.qcloud.com/trtc/webrtc/demo/detect/index.html?from_column=20420&from=20420

SimpleWebRTC 是一个基于 WebRTC 的 JavaScript 库，旨在简化实时通信的实现。本文将介绍如何使用 SimpleWebRTC 实现 P2P 文件传输。

<!-- more -->

## 1. 环境准备

### 1.1 引入 SimpleWebRTC

在你的 HTML 文件中引入 SimpleWebRTC 库。你可以通过 CDN 引入：

```html
<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>SimpleWebRTC P2P 文件传输</title>
    <script src="https://simplewebrtc.com/latest.js"></script>
</head>
<body>
    <div id="localVideo"></div>
    <div id="remoteVideos"></div>
    <input type="file" id="fileInput" />
    <button id="sendFileButton">发送文件</button>
    <script src="app.js"></script>
</body>
</html>
```

### 1.2 创建一个信令服务器

SimpleWebRTC 需要一个信令服务器来帮助建立连接。你可以使用 Node.js 和 Express 创建一个简单的服务器：

```bash
mkdir webrtc-demo
cd webrtc-demo
npm init -y
npm install express socket.io
```

创建 `server.js` 文件：

```javascript
// server.js
const express = require('express');
const http = require('http');
const socketIo = require('socket.io');

const app = express();
const server = http.createServer(app);
const io = socketIo(server);

app.use(express.static('public'));

io.on('connection', (socket) => {
    socket.on('message', (message) => {
        socket.broadcast.emit('message', message);
    });
});

server.listen(3000, () => {
    console.log('服务器在 http://localhost:3000 上运行');
});
```

将 HTML 文件放在 `public` 文件夹中。

## 2. 创建应用逻辑

在 `app.js` 中编写应用逻辑：

```javascript
// app.js
const webrtc = new SimpleWebRTC({
    localVideoEl: 'localVideo',
    remoteVideosEl: 'remoteVideos',
    autoRequestMedia: true,
    url: 'http://localhost:3000' // 信令服务器地址
});

// 当连接建立时
webrtc.on('readyToCall', () => {
    webrtc.joinRoom('my-room'); // 加入房间
});

// 处理远程视频流
webrtc.on('videoAdded', (video, peer) => {
    console.log('远程视频流已添加');
});

// 发送文件
const fileInput = document.getElementById('fileInput');
const sendFileButton = document.getElementById('sendFileButton');

sendFileButton.onclick = () => {
    const file = fileInput.files[0];
    if (file) {
        webrtc.sendToAll('file', file);
        console.log('文件已发送:', file.name);
    }
};

// 接收文件
webrtc.on('file', (file, peer) => {
    const url = URL.createObjectURL(file);
    const a = document.createElement('a');
    a.href = url;
    a.download = file.name;
    a.textContent = `下载文件: ${file.name}`;
    document.body.appendChild(a);
    console.log('接收到文件:', file.name);
});
```

## 3. 运行应用

在终端中运行服务器：

```bash
node server.js
```

然后在浏览器中访问 `http://localhost:3000`。打开多个浏览器窗口或标签页，你应该能够看到视频流的连接，并可以通过文件输入框发送文件。

## 4. 总结

通过以上步骤，你已经成功使用 SimpleWebRTC 实现了 P2P 文件传输功能。你可以根据需要扩展功能，例如添加聊天功能、文件传输进度等。

希望这篇指南能帮助你更好地理解和使用 SimpleWebRTC。如果你有任何问题或建议，欢迎在评论区留言。