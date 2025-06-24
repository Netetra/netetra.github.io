---
title: Rustで言語を自作してみた
tags:
  - Rust
---
# これはなに
言語名はPrimo、ChatGPTに決めてもらいました
動的型付けインタプリタ言語で、Rustで実装してます
https://github.com/Netetra/primo-lang.git
`programs/`以下にコード例があります

# 使用したcrate
- rust-peg
  - 構文のパースからAST生成までをしてます
- rstest
  - テスト書くとき使いました

# 文法
## 型
bool型、整数型、文字列型があります
`true` `false` `10` `"Hello"`

## 条件式
`==`と`!=`しか実装していません
Python同様`and`、`or`、`not`が使えます

## 変数定義
```
let x = 10;
```

## 代入
```
x = 20;
```

## print文
```
print "Hello World!", "\n";
```
`,`区切りで複数の値を出力できます

## if文
```
if hoge {}
elif huga {}
else {}
```

## loop文
Rustから輸入してきました
```
loop {
  next;
  break;
}
```

## 関数定義
```
fn add(a, b) {
  return a + b;
}
```


# 例
## Hello World!
```
fn main() {
	print "Hello World!";
}
```

## FizzBuzz
```
fn main() {
    let i = 1;
    let max = 100;

    loop {
        if i == max {
            break;
        }

        if i % 3 == 0 and i % 5 == 0 {
            print "FizzBuzz\n";
        }
        elif i % 3 == 0 {
            print "Fizz\n";
        }
        elif i % 5 == 0 {
            print "Buzz\n";
        }
        else {
            print i, "\n";
        }

        i = i + 1;
    }
}
```
