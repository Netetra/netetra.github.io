---
title: 2025年9月 最近設計した回路
tags:
  - 回路
---
# モタドラ
人生初モタドラ、燃えるかどうかは知らない
mosfetの駆動実験してるときに何個もマイコンを道連れにされたので絶縁した
![[/assets/image/dcmd_v1_circuit.png]]![[/assets/image/dcmd_v1_pcb.png]]

# サーボテスター
サーボ死んだか確かめるためにマイコンで毎度PWM出すのは非効率なので。
壊れたらロボ研の設計班でもはんだ付けして直せるように全部DIPです
![[/assets/image/servo_tester_v1_circuit.png]]
![[/assets/image/servo_tester_v1_pcb.png]]
# USB to UART
ロボコンでラズパイを殺されないようにするために作成
小型化を追求しすぎてリファレンスを載せれなかった
![[/assets/image/usb_to_uart_v1_circuit.png]]
![[/assets/image/usb_to_uart_v1_pcb.png]]
# Cytron MD10C
これは作ったわけではないけどノウハウを盗むためにテスターで回路図を起こしたもの
ne555の出力とコンデンサでブートストラップ作ってて面白い
![[/assets/image/cytron_md10c_circuit.png]]