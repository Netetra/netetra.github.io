---
title: CANの仕様について
tags:
  - "#protocol"
---
# CANとは
自動車用に開発された通信規格
- バス方式
	- 2本の通信線(それぞれ`CAN H` `CAN L`と呼ばれる)を使用して通信
	- 全てのノードは`CAN H`と`CAN L`に繋がる
- マルチマスター方式
	- 各ノードはいつでもデータを送信でき、他のノードが送信したデータをどのノードも受信できる


# 回路的な話
## ドミナントとレセッシブ
CANは差動伝送で`CAN H`と`CAN L`の電位の差で0と1を表現する
![[renesas_canbus_logic_level.png]]
*RenesasのCAN入門書より*

`CAN H``CAN L`に電位差が無いときをレセシブ、`CAN H``CAN L`に電位差があるときをドミナントと呼ぶ
レセシブは`0`、ドミナントは`1`

> [!TIPS]
> 差動伝送はペアとなる信号線、両方に同じノイズがのるため結局電位差は変わらないのでノイズに強い
> ![[/assets/image/vector_can_noise.png]]
> *vectorのはじめてのCAN/CAN FDより*

## CANバス
`CAN H`と`CAN L`のことをまとめてCANバスと呼び、

## コントローラーとトランシーバー
CANで通信するために殆どの場合はCANコントローラーとCANトランシーバーの2つを使用します
![[wiki_can_node.png]]
*wikipediaより*
### コントローラー
CANコントローラーはデータの送受信をマイコンの代わりに行います
マイコンから送りたいデータをセットするとあとは勝手に送信処理を行ってくれて、受信したデータはマイコンから好きなタイミングで取り出せます

### トランシーバー
CANトランシーバーはコントローラーとCANバスの間に位置し、0 1とドミナント レセシブの変換を行います
CANコントローラーからのTXピンが`High`ならCANバスをドミナントに、`Low`ならレセシブに。
CANバスがレセシブならトランシーバーのRXピンを`Low`に、ドミナントなら`High`にします

# フレーム


> 参考文献
> [Control Area Network](https://ja.wikipedia.org/wiki/Controller_Area_Network)
> [CAN入門書](/assets/pdf/RJJ05B0937-0100.pdf)
> [はじめてのCAN/CAN FD](https://cdn.vector.com/cms/content/know-how/VJ/PDF/For_Beginners_CAN_CANFD.pdf)