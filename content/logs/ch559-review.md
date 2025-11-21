---
tags:
  - CH559
  - 組み込み
  - 回路
title: CH559を使ってみて思ったこと
---
# 良いと思ったところ
## 1. 部品が少なくて済む
- UARTかUSBからの書き込みに対応
- 内部電源用の3.3Vを内蔵
により、USBからの給電にすればCH559とUSB端子とレギュレータ用のコンデンサの3つで動作可能なので回路が単純になる

## 2. CPUが8051ベース
8051は1980年にintelが作った物でこれに増築を重ねたのがCH559。
なので8051にあるペリフェラル周りは情報が結構転がってる
https://ja.wikipedia.org/wiki/Intel_8051

## 3. 秋月で売ってる
CH559自体には関係ないけど秋月で取り扱ってるので手に入れやすい
他だと公式がAliexpressで売ってるくらいだと思う

## 4. RAMもROMも結構ある
USBデバイスにするのでカーソルをくるくる回す嫌がらせマウスを作ってみたけどバイナリサイズは12KBくらい
- Arduino UNO R3
	- RAM: 2KB
	- ROM: 32KB
- CH559
	- iRAM: 256B
	- xRAM: 6KB
	- ROM: 64KB
でRAMもROMもArduino UNOより多いので当面尽きることは無さそう

# 悪いと思ったところ
## 1. CH559オリジナルのペリフェラルに関する情報が少ない
[[#良いと思ったところ]]で書いた通り8051にあるペリフェラルは情報が多いのですが、CH559に独自のペリフェラルは公式のKeilを使ってるサンプルコードか、データシートか、
https://github.com/atc1441/CH559sdccUSBHost
https://github.com/toyoshim/chlib
くらいしか参考になるものが無くて辛い

## 2. 謎にUARTが壊れる
UART0でデバッグ出力をして開発してて、別のペリフェラルいじってたら謎にUARTからの出力がバグって何をprintfしても同じバイト列を吐くようになる現象が起きてる。原因は不明でリファクタリングしたりしてると治る。開発しててこれ起きたらめんどくさい

## 3. 使えるコンパイラが少ない
調べた感じSDCCかKeilしか無い。Keilの方は環境が有料なので実質SDCCしかない
でもSDCCにはメモリアライメントする機能が無い。DMAのアドレス指定のレジスタで16bitのアラインメントしないといけないやつとかは
```c
#define DMA_BUFFER_SIZE 64
__xdata uint8_t __dma_buffer[DMA_BUFFER_SIZE + 1];
uint8_t* dma_buffer = __dma_buffer;
if ((uint16_t)dma_buffer & 1) {
  dma_buffer++;
}
```
と1byte多めにとって下のbit見てずらしたり
```c
#define DMA_BUFFER_SIZE 64
__at(0x0000) __xdata uint8_t dma_buffer[DMA_BUFFER_SIZE];
```
と直接アドレス指定する方法をみかけた

## 4. I2C無い
i2c無い。やりたかったらソフトウェア実装しかない
