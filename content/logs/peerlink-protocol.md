---
title: 自作通信規格PeerLink
tags:
  - C
  - Cpp
  - 組み込み
  - ESP32
---
# PeerLinkとは
ESP32シリーズで使えるEspressifの独自通信規格ESP Now。
アンテナつければ余裕で100m先でも1kBくらいのデータを1Mbpsで送受信可能なP2P型ネットワークの通信規格なんですけどネットワークを作るのに通信相手のMACアドレスが分かってないといけなくて、プログラム中にそれぞれのESP32のMACアドレスを埋め込むのがメンドイ

ので、そのP2Pネットワーク構築を楽にするための規格です。
PeerLinkの由来は単純にピア同士を自動で接続（Link）するのと早口で言うと**ペアリング**に聞こえなくもないからです。

# 実装
PlatformIOで使用できるライブラリとして以下にあります。
https://github.com/Netetra/peer-link

# Peer ID
PeerIDは1byteのピア識別子です。上位4bitが所属グループ、下位4bitがグループ内でのピアのIDを示します。
PeerLinkではESP32の元々のMACアドレスを上書きしローカル専用MACアドレスを使用してやり取りします。MACアドレスの6byteのうち先頭1byteは`0x02`固定で2~5byte目が所属ドメインの識別に使用されます。6byte目にPeerIDが挿入されます。

ドメインは構築する団体のことを表し、その団体内で個別のネットワークを構築する場合はグループを別にすることで分離できます。

実装ではデフォルトで
```cpp
const mac_addr_t PEER_BASE_MAC = { 0x02,  'n',  'n',  'c',  't', 0x00 }; // [5]: peer_id
```
と定義しています。
>ロボコンで使用するために作ったのでうちの高専名4文字をドメイン識別子として入れています。
>グループの分け方としてはAチームのESP32は`0xA?`BチームのESP32は`0xB?`という感じで良いと思います。
>なので他高専がもし使うならここを適宜書き換えて使用してください。逆にここを同じにするとうちのロボットを操作できてしまうので悪用しないでね

同一ドメインで同一グループであるピア同士は自動的にペアリングが行われます。
また`0xFF`は特別なPeerIDでブロードキャストを意味します。
実装では以下のように定義されています
```cpp
const peer_id_t BROADCAST_ID = 0xFF;
```
# Frame
基本構造としてフレームはSOFとフレームコードが先頭にあり、その後各フレーム固有のデータが連なります。
```
| SOF | frame_code | anything data ...
```
SOFは`0x55, 0xAA`
フレームコードは以下の表のとおりです

| フレーム            | 値    |
| --------------- | ---- |
| Beacon          | 0x01 |
| Pairing Request | 0x02 |
| Pairing Accept  | 0x03 |
| Message Frame   | 0x04 |

## Beacon Frame
ピアの生存確認と存在検知に使用します。500ms間隔で送信する必要があります
```
| SOF | frame_code |
```
## Pairing Request Frame
ピアの登録要求をします
`mac_addr`は登録したいピアのMACアドレスです。
```
| SOF | frame_code | mac_addr |
```
## Pairing Accept Frame
要求に対し許可の意味でこのフレームを返します
```
| SOF | frame_code | mac_addr |
```
`mac_addr`は要求元ピアのMACアドレスです。
## Message Frame
ピア間のデータの送受信をするフレームです。
一度に複数種類のデータを連ねる形で送信できます。
```
| SOF | frame_code | msg.type | msg.len | msg.data | msg.type ....
```

複数送信する場合は`frame_code`の後ろに
```
| msg.type | msg.len | msg.data |
```
の部分がn個連なる形になります

メッセージの`type`や`data`の構造は各々で決めてください。
# ペアリング・通信・ピア管理方式の図
![[/assets/image/peerlink_chart.png]]
![[/assets/image/peerlink_before_pairing_phase.png]]
![[/assets/image/peerlink_after_pairing_phase.png]]
![[/assets/image/peerlink_pairing_cancel.png]]

# コード例
ロボコンのコントローラーの実装です
PS4コントローラーとUSBで接続し、コントローラーの値を50msごとにPeerLinkでブロードキャストします
https://github.com/Netetra/robocon-esp32s3-controller
