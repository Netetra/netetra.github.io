---
title: 385用のMDを作った
tags:
  - 回路
---

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">動きました。やったね！！ <a href="https://t.co/0v9l1rCPR7">https://t.co/0v9l1rCPR7</a> <a href="https://t.co/Jbym8YPlSb">pic.twitter.com/Jbym8YPlSb</a></p>&mdash; Nano (@Nano_masuter) <a href="https://twitter.com/Nano_masuter/status/1988219582356877702?ref_src=twsrc%5Etfw">November 11, 2025</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>
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

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">逆接保護と静電気保護、電流制限（tb67h450の機能）を詰めました <a href="https://t.co/ncGjRJKNah">pic.twitter.com/ncGjRJKNah</a></p>&mdash; Netetra (@netetra07) <a href="https://twitter.com/netetra07/status/2009566982501441931?ref_src=twsrc%5Etfw">January 9, 2026</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>
