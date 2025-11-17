---
title: CH559でUSBマウス
tags:
  - 組み込み
  - CH559
  - USB
---
CH559でUSBデバイスを作ってみる。とりあえずマウス

# 回路
USBデバイスと後々USBホストをしたいのでブレッドボードでは辛いので基板を制作
本当はTVSダイオード付けたりFullSpeed出せるからインピーダンス整合とか考えなければならない気がする
![[/assets/image/ch559_dev_board_v1_circuit.png]]![[/assets/image/ch559_dev_board_v1_pcb.png]]
![[/assets/image/ch559_dev_board_v1.png]]
ピン配置の違うAZ1117になってたので違法建築で修正
![[/assets/image/ch559_dev_board_v1_az1117_miss.png]]

# プログラム
参考資料:
- https://www.wch.cn/products/CH559.html
- https://qiita.com/toyoshim/items/47df7684067abfe2273d
- https://github.com/toyoshim/chlib
- https://github.com/wagiminator/CH552-MouseWiggler

ディスクリプタのやりとりは知ってるけど、その下の層のパケット単位でのやりとりは無知すぎて結構苦戦しましたが
![[/assets/movie/ch559_usbd_mouse_circle_linux.mov]]
とりあえずカーソルをくるくる回す嫌がらせデバイスが完成！

と思いきや、windowsに刺すと動かない。デバイスマネージャーにはちゃんとHID準拠マウスって出てくるのに...
まぁwindowsだから動かなくてもしかたないか、とwindowsアンチなので無視することにしようとしましたがやっぱり気になるので調査

Wiresharkで通信を覗いてみる
![[/assets/image/ch559_usbd_mouse_wireshark.png]]
ディスクリプタの受け渡しが終わった後ちゃんとEndpoint 1のINにレポートを要求してレポート返してるけど、なんか合間合間にストールがどうたら言ってる

## 原因
`UEPn_CTRL`レジスタの`bUEP_R_TOG`と`bUEP_T_TOG`、DATA0とDATA1を決めるって書いててなにこれって思って適当に入れてました。
データパケットにはDATA0とDATA1があり送るたびに切り替えるらしいくこれでパケットの欠落を検知するとか。
Endpoint0で行われるControl転送は送るデータのうちの最初はDATA0で余りがあるならその後は交互にする感じで、マウスのレポートは3byteだから一回で終わるのでずっとDATA0でいいと思ってました。
データの区切り関係なくデータパケットを送るたびに切り替えるらしい

つまりDATA0しか送ってなかったのでwindowsは半分くらいパケットをロストしてるとしてレポートを受け取ってもカーソルの動きに反映しなかった？linuxでは動いたけどlinuxのドライバがゆるいのかな？
とりあえず挙動としてはwindowsのほうが正しそう。今回は疑ってごめんね？

![[/assets/movie/ch559_usbd_mouse_circle_windows.mov]]
`bUEP_T_TOG`をトグルするようにしたらちゃんとwindowsでもカーソル動きました

あとちょくちょくカーソルが止まる問題とレポートを受け付けない問題がありましたが、
カーソル止まる問題はwiresharkで見ると`URB_FUNCTION_ABORT_PIPE`と出てました
10msにするとなくなったので多分応答が間に合ってなかったのかな
レポート受け付けない問題は、今回xの変位とyの変位以外送ること無いのでHIDレポートディスクリプタを標準マウスのやつからボタンをもぎ取った形に改変してたら、それがダメだったらしく、ボタンも含めて3byte送れば受け取りました

ロジックアナライザ欲しい

最終的なコードはこちら
https://github.com/Netetra/ch55x-template/blob/main/examples/usbd_mouse.c

これ書いてて見つけたけどプロトコルはこれが分かりやすそう
https://avr.jp/user/EC/PDF/USBspcs.pdf