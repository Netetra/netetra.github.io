---
title: 2025年9月の出来事
tags:
  - 日記
  - Python
---
# 18日
## poetryがpyenvで入れたバージョンのpythonを使ってくれない件

ホストに入ってるバージョンが3.13、プロジェクトで使いたいのは3.11
```fish
$ pyenv install 3.11
$ pyenv global 3.11
$ poetry env use 3.11
$ eval (poetry env activate)
$ python --version
3.13.7
```
なぜか変わらない

いろいろ試した結果Archの公式リポジトリにある`python-poetry`を使ってたのがダメだったのか、公式インストーラーでインストールしたら普通にできた
```
curl -sSL https://install.python-poetry.org | python3 -
```
結局何が原因なのかは謎