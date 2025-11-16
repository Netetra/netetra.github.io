---
title: CH559のクロック周り
tags:
  - 組み込み
  - CH559
---
[これ](./ch559-led-blink) に続いてクロック周りを触ります。

# クロック周りの構成
![[/assets/image/ch559_clock_diagram.png]]
_CH559のデータシートより_

クロックの供給元は12MHzの内蔵オシレーターか外部水晶振動子の二択で、PLLで倍の周波数にした後にUSB用とその他のクロックに別れる。それぞれ分周器がついており、各ペリフェラルにも分周器がついてる。

STM32とかとそんなに変わらないですね

# レジスタ
クロックに関わるレジスタは`CLOCK_CFG`と`PLL_CFG`の２つ
![[/assets/image/ch559_clock_cfg_reg.png]]
![[/assets/image/ch559_pll_cfg_reg.png]]

## セーフモードとは
いくつかのレジスタはセーフモードに入らないと書き換えられないように保護されてるとのこと
セーフモードへの入り方は
1. `SAFE_MOD`レジスタに`0x55`を書き込む
2. `SAFE_MOD`レジスタに`0xAA`を書き込む
この操作をした後、13〜23サイクルはセーフモードでその後通常モードになる。
もしくは任意の値を`SAFE_MOD`に書き込むことでも出られる

# プログラム
まずはセーフモードへ入るための関数を用意
```c
inline void __enter_safe_mode() {
  SAFE_MOD_REG = 0x55;
  SAFE_MOD_REG = 0xAA;
}

inline void __exit_safe_mode() {
  SAFE_MOD_REG = 0xFF;
}
```

そしてクロックの設定を変える関数も用意
```c
inline void clock_init(uint8_t pll_mul, uint8_t fusb_div, uint8_t fsys_div) {
  __enter_safe_mode();
  PLL_CFG_REG = 0;
  PLL_CFG_REG |= (fusb_div << 5) | pll_mul;
  
  CLOCK_CFG_REG &= 0b11100000;
  CLOCK_CFG_REG |= fsys_div;
  __exit_safe_mode();
}
```

これでクロックを変え放題...と思いきや`clock_init`の`inline`を外すとクロック設定が変わらない...
`inline`つけて壊れるのは分かるけど外すと壊れるってどういうこと？

考えてもわからなかったので出力されたアセンブリを見てみると![[/assets/image/ch559_safe_mode_asm.png]]
_左: inlineあり, 右: inlineなし_

なんか処理長くね？もしかして13〜23サイクル超えてる？

## 結論

```c
void clock_init(uint8_t pll_mul, uint8_t fusb_div, uint8_t fsys_div) {
  uint8_t tmp = (fusb_div << 5) | pll_mul;
  
  __enter_safe_mode();
  PLL_CFG_REG = tmp;
  
  CLOCK_CFG_REG &= 0b11100000;
  CLOCK_CFG_REG |= fsys_div;
  __exit_safe_mode();
}
```
書き込む値の計算を終わらせておき、セーフモード内でする処理を少なくしたら動きました

![[/assets/image/ch559_clock_howto_change.png]]
まぁデータシートにそうしろって最初から書かれてたんですけど...
データシートはちゃんと読め(戒め)

コードはgithubに上げました
https://github.com/Netetra/ch55x-template