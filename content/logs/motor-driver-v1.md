---
title: モタドラを作ってみた
tags:
  - 回路
---
# 設計
人生初モタドラ、燃えるかどうかは知らない
mosfetの駆動実験してるときに何個もマイコンを道連れにされたので絶縁した

## 定格
| 項目  | 値                 |
| --- | ----------------- |
| 電圧  | max 20V（ゲートの耐圧）   |
| 電流  | 10A               |
| PWM | 20kHz（Duty 99%まで） |
として設計しました
![[/assets/image/dcmd_v1_circuit.png]]
![[/assets/image/dcmd_v1_pcb.png]]
# 回してみる
![[/assets/image/dcmd_v1_jlcpcb.png]]
黒基板いい感じ。JLCPCBに発注しました

はんだ付けをして775を繋いだ
![[/assets/movie/dcmd_v1_775.mov]]
この後逆起電力に耐えれるかのチェックでDuty99%で回して反対方向に回したら燃えました

## 燃えた原因
![[/assets/image/dcmd_v1_design_miss1.png]]
A相の配線はビアで裏面のベタを通してXT30までつなげてたのですが、ベタの後に通したゲートの配線が横切ってて1mmも無いくらい細くてパターンが焼ききれてました
![[/assets/image/dcmd_v1_design_miss2.png]]
もう一つ、はんだ付け後にLEDが光らなくてLEDのGNDがどこにも繋がってないというミスも

![[/assets/image/dcmd_v1_repaired.png]]
修復完了！

![[/assets/movie/dcmd_v1_735.mov]]
735の逆起電力に耐えてる絵
![[/assets/image/dcmd_v1_running_10a.png]]
定常10Aをかけてる絵

初めて作ったMDですが設計ミスも2つくらいで絶縁されてて、ちゃんと決めた定格通りに動いてくれて735の逆起電力で死ななかったので良しとします