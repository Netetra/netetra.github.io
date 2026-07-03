---
tags:
  - 回路
  - Linux
title: NVMeが死んだThinkpadを生き永らえさせる
---
# 手が滑って落としちゃってthinkpadが死にました。
NVMe周辺回路のみがピンポイントに死ぬとかいう謎の死に方で。
修理見積もりしたところ最低でも9万、新品を買うならX1 Carbonが欲しい

# USB bootでとりあえず耐える
![[/assets/image/opened-thinkpad.png]]
無理やり筐体内にスペースを作ってUSBメモリをねじ込んでUSB bootで運用することに
流石にファンの上をシールド無しでUSB3.2の線を通したらエニュメレーション段階で失敗しちゃったのでこのあとUSB2.0の線以外切断しました

この状態で2週間ほど運用してましたが、段々と速度が低下していって最終的には何するのも数分フリーズするように...
WWANカードのところはホワイトリストで制限かけられてるからSSDさせないし、他にPCIeで繋がるようなとこ無いし、かといってこのままUSB bootで運用するにしてもUSBメモリは速度が限界だし、USBメモリ型SSDなら速度は期待できるけどお金ないし相性問題で起動できないとかありそうだしどうしたら...

↓これはAポートがあった基板が新品4000円で買いたくないので自作しようと試みてるメモ
![[/assets/image/thinkpad-a-port-circuit.png]]
![[/assets/image/thinkpad-a-port-circuit-kicad.png]]

# ROMが無いならRAMを使えば良いじゃない？
/の全てをRAMに置けば良いのでは？

というわけで置きました
![[/assets/image/thiknpad-filesystem-list.png]]

## 仕組み
`/etc/initcpio/hooks/copytoram`
```
run_latehook() {
        mkdir /ram_root

        mount -t tmpfs -o size=13G tmpfs /ram_root

        echo "Copying root filesystem to RAM..."

        rsync -aHAXx \
                --info=progress2 \
                --stats \
                --exclude=/proc \
                --exclude=/sys \
                --exclude=/dev \
                --exclude=/run \
                --exclude=/tmp \
                /new_root/ /ram_root/

        umount /new_root
        mount --move /ram_root /new_root

        echo "Switched RAM root filesystem."
        sleep 3
}
```
`/etc/initcpio/install/copytoram`
```
build() {
        add_runscript
        add_binary rsync
}

help() {
        echo EOF
root filesystem copy to RAM.
EOF
}
```

mkinitcpioのhookでinitramfs内で本物のrootfsをマウント後にtmpfsでRAM上にrootfsと同じサイズのディスクを作成、rsyncで本物のrootfsをコピーし終わったら本物rootfsをアンマウントし本物のrootfsがマウントされてたところに偽物のRAM上のrootfsをマウントとして起動中にrootfsをすり替えてます

\> でも再起動したらデータ消えるのでは？

電源オフ時にUSBメモリにRAM上のrootfsを書き戻すようにしてあるので問題ありません
`/usr/lib/systemd/system/copytorom.service`
```
[Unit]
Description=Sync RAM to root filesystem.
DefaultDependencies=no
Before=shutdown.target
RefuseManualStart=true

[Service]
Type=oneshot
ExecStart=/usr/local/bin/copytorom

StandardOutput=journal+console
StandardError=journal+console

[Install]
WantedBy=shutdown.target
```
`/usr/local/bin/copytorom`
```bash
#!/bin/bash

set -eu -o pipefail

mkdir -p /run/copytorom
mount /dev/sda2 /run/copytorom

echo "Syncing RAM to root filesystem..."

rsync -aHAXx --delete \
        --stats \
        --info=progress2 \
        --exclude=/proc \
        --exclude=/sys \
        --exclude=/dev \
        --exclude=/run \
        --exclude=/tmp \
        / /run/copytorom

umount /run/copytorom

echo "Complated rsync!"
sleep 3
```

なんならこうすることでUSBメモリへの書き込みはその時に全て行われるためUSBメモリの寿命向上ができ都合が良いです

メモリが16GBのため/homeと/varは含めるのを諦めパーティションを分けており、RAM上に置いてません
今はメモリが高騰してるので増設したらrootfsを拡張したい

現在の問題はUSB2.0接続のままなので起動時に読み出し終えるまでに5分かかることですね
3.2で接続できてかつ、本体内に収めれる方法を模索中
