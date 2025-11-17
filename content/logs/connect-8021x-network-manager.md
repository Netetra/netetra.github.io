---
tags:
  - Linux
  - Network
title: NetworkManagerで802.1x認証のネットワークに繋ぐ
---

これはとある高専の寮の中で使えるネットワークに繋ぐメモです。

# 方法

<適当な名前>.nmconnectionに以下を記述

```
[connection]
id=<適当なid>
uuid=<適当なuuid>
type=<接続方式>
interface-name=<デバイス名>

[ethernet]

[802-1x]
eap=peap;
identity=<ユーザー名>
password=<パスワード>
phase2-auth=mschapv2

[ipv4]
method=auto
```

## 追記

wpa_supplicantから下記のエラーが出てた

```
00:54:59 wpa_supplicant: enp2s0: CTRL-EVENT-EAP-FAILURE EAP authentication failed
00:54:58 wpa_supplicant: OpenSSL: openssl_handshake - SSL_connect error:0A000102:SSL routines::unsupported protocol
00:54:58 wpa_supplicant: SSL: SSL3 alert: write (local SSL3 detected an error):fatal:protocol version
00:54:58 wpa_supplicant: enp2s0: CTRL-EVENT-EAP-METHOD EAP vendor 0 method 25 (PEAP) selected
00:54:58 wpa_supplicant: enp2s0: CTRL-EVENT-EAP-PROPOSED-METHOD vendor=0 method=25
00:54:58 wpa_supplicant: enp2s0: CTRL-EVENT-EAP-PROPOSED-METHOD vendor=0 method=13 -> NAK
00:54:58 wpa_supplicant: enp2s0: CTRL-EVENT-EAP-STARTED EAP authentication started
```

### エラーの原因

調べた感じ認証サーバー側はTLS1.0 TLS1.1を使用してるけどTLS1.0 TLS1.1に脆弱性があって、wpa_supplicantのサポートしてるプロトコルからデフォルトだと除外されてるからっぽい

### 解決方法

認証サーバー側でTLS1.2以上を使用するようにすればいいと思うけど
今回、僕は認証サーバーに変更を加えれないのでNetworkManager側でどうにかする

NetworkManagerのドキュメントを眺めてたら802.1x認証の設定で`phase1-auth-flags`という
オプションを発見
wpa_supplicantに認証時のフラグを渡すオプションぽい
フラグはbit演算で管理されてて引数には32bit整数型を渡す
https://lazka.github.io/pgi-docs/index.html#NM-1.0/classes/Setting8021x.html#NM.Setting8021x.props.phase1_auth_flags

フラグ一覧はこれ
https://lazka.github.io/pgi-docs/NM-1.0/flags.html#NM.Setting8021xAuthFlags

今回はTLS1.0とTLS1.1の無効化を解除したいので`TLS_1_0_ENABLE`と`TLS_1_1_ENABLE`を立てる
32 + 64 = 96で
`/etc/NetworkManager/<connection name>.nmconnection`の`[802-1x]`フィールドに

```
phase1-auth-flags=96
```

を追記すれば繋がった

## さらに追記
多分校内の機器が更新されたのでフラグを立てる必要は無くなりました