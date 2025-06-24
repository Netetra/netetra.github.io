---
title: 組み込みにおけるデバッガー周辺知識の整理
tags:
  - 組み込み
---

# 全体の構成

![[/assets/image/debugger_block_diagram.png]]

## デバッグプローブ

マイコンとPCの間の橋渡し役の機器

- J-Link
- ST-Link
- DAP-Link
- CMSIS-DAP
  など、いくつか通信規格が存在する

### マイコンとデバッグプローブ間の接続方式

- JTAG
  - TCK、TDI、TDO、TMSの4線
- SWD
  - SWDIOとSWCLKの2線

## OpenOCD

Open On-Chip Debuggerの略
GDB単体では外部にあるプログラムを操作できないので、GDBの代わりにデバッグプローブとの通信を担当する橋渡し役

## GDB

GNU Debuggerの略
プログラムを途中で止めたり、変数の中身をリアルタイムで見れたりする
本来はローカルで動くプログラムのデバッグ用。外部で動いてるプログラムをデバッグするための機能もあり、これを用いてOpenOCDとやりとりする形でマイコン上で動いてるプログラムをデバッグしている

GDBはあくまで操作を受け付けるサーバーで、実際にデバッグするときはVSCodeなどのGDBに命令を出すクライアントが必要。
