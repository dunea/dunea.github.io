---
title: Bsky 标签机开发要点记录 & 包含常用的 Bsky Api 接口
date: 2026-03-05 20:16:17
categories:
    - [Bsky, Bsky Labeler]
tags:
    - Bsky Labeler
    - BlueSky 标签机开发
    - BlueSky 标签机接口
---

本文记录了 Bsky 标签机开发的要点。介绍了标签机需要调用的核心接口，包括 .well-known/did.json、com.atproto.label.queryLabels 查询接口和 com.atproto.label.subscribeLabels WebSocket 订阅接口。列举了开发中常用的辅助工具和查询方式。阐述了标签机工作流程：订阅 Firehose 接收新内容、数据库存储待标记项、响应 AppView 的标签拉取请求、最后配置账号为标签机。建议参考官方文档和开源示例深入学习。

<!-- more -->

---

### 标签机需要用的接口

```shell
# .well-known/did.json
https://sexy-guard.inbsky.com/.well-known/did.json

# com.atproto.label.queryLabels 接口
https://sexy-guard.inbsky.com/xrpc/com.atproto.label.queryLabels?uri=at://did:plc:tjgtf5lcmjgj465zdc36dson/app.bsky.feed.post/3mgb6lgwm2s26&limit=50

# com.atproto.label.subscribeLabels wss 接口
wss://sexy-guard.inbsky.com/xrpc/com.atproto.label.subscribeLabels
```

---

### 辅助网站或者接口

```shell
# ws 在线测试
http://websocket.tool0.com/
https://tool.lu/wstester/

# bsky plc 查询接口
https://plc.directory/did:plc:jcqd5bsmymiug5sutbwikihs

# 帖子或者用户标签查询，能查询所有标签机
https://blue.mackuba.eu/scanner/

# 查询 bsky handle did api 接口
https://bsky.social/xrpc/com.atproto.identity.resolveHandle?handle=xfreeson.bsky.social # 直接调用resolveHandle接口

https://bsky.social/xrpc/app.bsky.feed.searchPosts?q=from:xfreeson.bsky.social # 或者使用搜索API

# 标签机官方文档
https://atproto.com/guides/labels
https://atproto.com/guides/creating-a-labeler
```

---

### 标签机流程：

1. 订阅 bsky firehose 接收新建帖子、用户变动这些信息。
2. 记录需要标记的帖子、用户，可以存到 redis、mysql 这些数据库。
3. 等待 bsky 的 AppView 请求 ws 接口拉取标记的记录和标签。
4. 把账号设置成标签机。

别的可以看看官方文档，DOCS Moderation 里面有详细介绍，也可以 GitHub 上搜搜别人标签机的例子。
