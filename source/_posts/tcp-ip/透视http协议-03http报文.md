---
title: 透视http协议-03http报文
date: 2021-05-31 17:39:53
tags: 
	- http
	- tcp
cover: /images/http.jpg
categories: tcp-ip
typora-root-url: ../..
---

# HTTP报文

**HTTP 协议的请求报文和响应报文的结构基本相同，由三大部分组成：**

1. 起始行（start line）：描述请求或响应的基本信息；
2. 头部字段集合（header）：使用 key-value 形式更详细地说明报文；
3. 消息正文（entity）：实际传输的数据，它不一定是纯文本，可以是图片、视频等二进制数据。
