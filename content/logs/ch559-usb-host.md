---
title: CH559でUSBホスト
tags:
  - CH559
  - C
  - USB
  - 組み込み
---
USBホストしてマウスからデータを取ってみる

# 回路
[[./ch559-usbd-mouse]]と同じ

# プログラム
大体こんな感じ
https://github.com/Netetra/ch55x-template/blob/main/examples/usbh_mouse_poll.c

# 動作
レポートをそのまま16進数で出してる動画
![[/assets/movie/ch559_usbh_2mouse_read.mov]]

https://github.com/Netetra/ch55x-template/commit/78fca050e050824f1c68f6f57510e78703fbe8a8
https://github.com/Netetra/ch55x-template/commit/af68fbd70b83d22a5c6871e9814b7c59eb29b3ed
この2つが原因で結構詰まった。三回くらいデータシートと見比べて確認したんだけどな...

これからはUSBデバイスからデータを取れるので色々夢が広がる