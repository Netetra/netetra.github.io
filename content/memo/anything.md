---
title: まとめてない色々
---
- probe-rsはudevルールを入れてから使う
- pacman経由のpoetryと公式のスクリプトで入るpoetryは微妙に違う
- 以下のコードは一見、コンパイル通らなさそうで通る
```c
int main() {
  void (*hoge)(void) = hoge;
  hoge();
  return 0;
}
```
- 