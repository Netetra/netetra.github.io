---
title: iptables置き場
tags:
  - Linux
  - Network
---


Dockerコンテナ内からはホストのLAN内の機器は見えなくするルール
```
*filter
:INPUT DROP [0:0]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [781:72494]
-A INPUT -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
-A INPUT -i lo -j ACCEPT
# ・・・
# 192.168.*.*行きのパケットをドロップ
-A FORWARD -d 192.168.0.0/16 -i docker0 -j DROP
COMMIT
```