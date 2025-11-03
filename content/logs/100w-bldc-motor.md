---
title: BLDCを回してみる
tags:
  - 回路
  - 組み込み
---
https://www.orientalmotor.co.jp/ja/products/detail?hinmei=BLHM5100K-30
部室にオリエンタルのデカいブラシレスが居たのでBLDCに入門してみようと思います
# 設計
![[/assets/image/100w_bldc_md_circuit.png]]
![[/assets/image/100w_bldc_md_pcb.png]]
これをLPKFで3つ削り出して、はんだ付けすれば
![[/assets/image/100w_bldc_md_3piece.png]]
付いてるmosfetは100V100A定格であまりにもオーバースペックなハーフブリッジができた

# 回してみる
エンコーダーが付いてるのですが取説には説明が一切無かったので、このモーターが都合よく2ついたので片方をバラしてみることに
![[/assets/image/100w_bldc_sensor_pcb.png]]
![[/assets/image/100w_bldc_md_encoder.png]]
結果、センサはホールセンサで緑がGNDで黄色が電源、残りが3相分のオープンコレクタ出力と判明
あとは適当にコード書いて、繋げれば
![[/assets/movie/100w_bldc.mov]]
回りました

コードはこちら
https://github.com/netetra/bldc-test