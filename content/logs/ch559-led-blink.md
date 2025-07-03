---
title: CH559でLチカしてみる
tags:
  - 組み込み
  - CH559
---
# CH559とは
CH559は[江蘇沁恒股分有限公司(WCH)](https://www.wch-ic.com/)が作ってるUSBホスト・デバイスの機能がくっついてる8bitマイコンで、コアは[8051](https://ja.wikipedia.org/wiki/Intel_8051)とかいう1980年に作られたCPUを拡張したものらしい

日本では秋月で売ってます
- [CH559T](https://akizukidenshi.com/catalog/g/g118027/)
- [CH559L](https://akizukidenshi.com/catalog/g/g116307/)

# 回路
- リセットは珍しくLowにすると入るらしい
- P4.6はブートモードの選択で起動時にLowならブートローダーに入り書き込みができるようになる
- 内部にレギュレータが内蔵されてて、外部にレギュレータを載せないならVDD33をデカップリングしなくちゃだめらしい


![[/assets/image/ch559-led-blink-schematics.png]]
![[/assets/image/ch559-led-blink-breadboard.png]]
_実際に組んだところ_

# プログラム
