---
title: C++のライブラリの作り方
tags:
  - Cpp
---
# はじめに
この記事ではArduinoでLEDを操作するライブラリを例とします

# 1. ヘッダーファイルの用意
まずヘッダーファイルを作ります。名前は適当に`led.h`とかで
作れたら以下を書きましょう
```cpp
#ifndef LED_H
#define LED_H

#endif
```
これはインクルードガードと呼ばれるもので、これを書かないと何回もインクルードされたとき、ヘッダーファイル内で定義してある関数やクラスが複数回定義されます

# 2. クラス定義
クラスを定義します
```cpp
#ifndef LED_H
#define LED_H

class Led {
  public:
    enum class State {
      Off,
      On
    };
    Led(int pin, State default_state);
    void on();
    void off();
    void set(State state);
    void toggle();
    void set_default();
  private:
    int pin;
    State state;
    State default_state;
};

#endif
```
これで`led.h`側は完成です。クラスのメソッドの中身は次の手順で書きます
# 3. 実装
`led.h`と同じフォルダ内に`led.cpp`を作ってください
作れたら`led.h`をインクルードしてクラスのメソッドの中身を書きます
```cpp
#include "led.h"
#include <Arduino.h>

Led::Led(int pin, Led::State default_state) {
  this->pin = pin;
  this->state = default_state;
  this->default_state = default_state;
  this->set_default();
}

void Led::on() {
  this->state = Led::State::On;
  digitalWrite(this->pin, HIGH);
}

void Led::off() {
  this->state = Led::State::Off;
  digitalWrite(this->pin, LOW);
}

void Led::set(State state) {
  if (state == Led::State::On) {
    this->on();
  } else {
    this->off();
  }
}

void Led::toggle() {
  if (this->state == Led::State::On) {
    this->off();
  } else {
    this->on();
  }
}

void Led::set_default() {
  this->set(this->default_state);
}
```

# 4. 完成
たったこれだけです。あとはGithubで公開するなりなんなり
