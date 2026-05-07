---
title: 385用のMDを作った
tags:
  - 回路
---
https://x.com/Nano_masuter/status/1988219582356877702?s=20
このツイートを見て作りたくなり作りました

# 回路
## 回路図
![[/assets/image/dcmd_rs385_v1_schematic.png]]
## PCB
![[/assets/image/dcmd_rs385_pcb_f.png]]![[/assets/image/dcmd_rs385_pcb_b.png]]

機能としては
- 逆接保護
- 静電気保護
- お祈りサージ対策
- 3Aの電流制限
- CytronのMDと同じ制御方式

モーターからのノイズとかが怖かったので裏面全体を電磁シールド的な機能をしてくれないかと期待して殆ど片面縛りみたいな配線をしました。
端子間につけるセラミックコンデンサも基板上に実装されてるのでモーター端子にはんだ付けするだけで使用可能です。

# 実物

![](https://x.com/i/status/2009566982501441931)