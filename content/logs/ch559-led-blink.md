---
title: CH559で遊ぶ (1) Lチカ
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


![[ch559_led_blink_schematics.png]]
![[ch559_led_blink_breadboard.png]]
_実際に組んだところ_

# プログラム
```c
#include <stdint.h>
#include "ch559.h"

void main() {
	PORT_CFG_REG = 0b00101101;
	P1_DIR_REG = 0b00000100;
	P1_PU_REG = 0b11111011;
	P1_REG = 0x00;

	while (1) {
		P1_REG = (!(P1_REG & 0b00000100)) << 2;
		for (uint32_t i = 0; i < (100000UL); i++) {
			__asm__("nop");
		}
	}
}
```

# 書き込み
P4.6をLowにしながらPCにUSBで接続。Rust製の書き込みツール`wchisp`で書き込みます
https://github.com/ch32-rs/wchisp

```sh
wchisp flash ./out/main.hex
```

![[ch559_led_blink.gif]]

コードはgithubに上げました
https://github.com/Netetra/ch55x-template
