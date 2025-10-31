---
tags:
  - 回路
title: USB-UART変換基板作ってみた
---
新居浜高専Aチームは今年もタブレットでの操作がしたいとのこと
ラズパイのGPIOで去年はやりましたが、GPIOにアクセスする手段が少ないのでもっと普及した安定する方式は無いかなーてことでUSB CDC ACMが良さそうなのでUSB to UARTの基板を作りました

# 設計
![[/assets/image/usb_to_uart_v1_circuit.png]]
![[/assets/image/usb_to_uart_v1_pcb.png]]
小型化を優先しすぎてリファレンスを載せれなかった
ロボットに735が乗ってて怖いので絶縁してます
過電流、逆接、静電気の保護つき

# はんだ付け
![[/assets/image/usb_uart_v1_stand_capacitor.png]]
慣れてないステンシル、リフロー中に立ち上がるコンデンサと格闘

![[/assets/image/usb_uart_v1_2piece.png]]
どうにか予備分も完成

![[/assets/image/usb_uart_v1_broken.png]]
試走中にきれいにもげたTypec（ランドごともっていかれなくてよかった）
![[/assets/image/usb_uart_v1_repaired.png]]
予備を使ってる間に修復

![[/assets/image/usb_uart_v1_using.png]]
この後はロボコン終わるまで何事も無かった（ﾖｶｯﾀﾖｶｯﾀ）