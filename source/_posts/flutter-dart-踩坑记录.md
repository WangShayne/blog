---
title: flutter - dart 踩坑记录
date: 2022-08-02 14:18:49
tags:
---



##  E/DartVM  ( 3798): WebSocketException: Invalid WebSocket upgrade request

###  因设置了http_proxy 和 https_proxy. 再设置 NO_PROXY = localhost,127.0.0.1 即可


