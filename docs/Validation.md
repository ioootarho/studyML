---
layout: default
title: 09, バリデーションスキーム
nav_order: 10
math: mathjax3
---

# バリデーションスキーム
{: .no_toc }

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---


機械学習モデルを作って、その性能を評価するときに避けて通れないのがばリデーションスキーム。  
例えば学校の勉強で...
- 問題集を解く
- 答え合わせをする
- 間違えたところをもう一度解く
- 答え合わせをする
- これを繰り返す

このとき、どの程度身についたかを測るために同じ問題集を解かせるか？  
&rarr; No!  
なぜなら、同じ問題集を解いても答えを知っているのでみんな高得点がとれてしまう。  
そこで、普通は新たに用意した別の問題を解かせて、その点数で測る。  
  
機械学習モデルにも同じことが言える。  
しかし、現実には必ずしも新しい別の問題（サンプル）がすぐ手に入るとは限らない。  
そこで、今手元にある問題（サンプル）を学習用と性能評価用に分割する。  
学習用サンプルでモデルを作り、性能評価用サンプルでその性能を測ることにする。  
では、学習用と性能評価用をどう分割すると正確に性能を測れるだろうか？  
&rarr; いろいろな分割の枠組み、分割方法が考案されている。  

分割の枠組みをバリデーションスキームと呼ぶ。  
よく用いられるバリデーションスキームは以下の3つ。

## ホールドアウト

ホールドアウトとは、データの一部分だけをとっておくこと。  
Leave One Out (LOO) と呼ぶこともある。  

![figure LeaveOneOut](./figures/LeaveOneOut.png)

## クロスバリデーション

クロスバリデーションとは、場所を変えて複数回Leave One Out すること。  
よくCVと略される。  
クロスバリデーション中の個々のLeave One Out をfold と呼ぶ。  

![figure CrossValidation](./figures/CrossValidation.png)

## バックテスト

バックテストとは、時間軸のあるデータにおいて古い部分をtrainに、新しい部分をvalidationに割り当てること。  
時系列に沿った推移を分析するときに用いる。  

![figure BackTest](./figures/BackTest.png)

## データの分け方

バリデーションスキームを決めたら、具体的にどう分割するかを決める。  
これをパーティショニングと呼ぶこともある。  
バリデーションスキームやデータの中身によって、採用すべきパーティショニングが異なってくるので注意が必要。  
よく用いられるパーティショニングは以下の4つ。

### K-fold

とりあえずサンプルを順番にtrainとvalidationに割り振る。  
- 時系列データには使えない
- 回帰問題でも分類問題でもOK

![figure KFold](./figures/KFold.png)  

実務上は順番に割り振るのではなく、ランダムに割り振る。  

![figure KFoldShuffle](./figures/KFoldShuffle.png)

### Stratified K-fold

層化抽出のこと。  
- 時系列データには使えない
- 回帰問題には使えない
- 分類問題のみOK

![figure StratifiedKFold](./figures/StratifiedKFold.png)

これも実務上は順番に層化抽出するのではなく、ランダムに層化抽出する。  

![figure StratifiedKFoldShuffle](./figures/StratifiedKFoldShuffle.png)

### タイムシリーズパーティション

時系列データのときに用いる。

![figure TimeSeriesSplit](./figures/TimeSeriesSplit.png)

### グループパーティション

実はサンプルの背後にグループが存在するような時に用いる。  
例えば...
- 社員の退職予測を行いたい
- 過去2年分のデータを用いる
- 毎月取っている属性や勤怠、人事考課のデータを用いて、その翌半年以内に退職したら1、しなかったら0とラベルを振る  
  &rarr; 1レコード1社員だが、2年間在籍している社員は4レコード登場する  
- Aさんは2年間在籍していたので4レコード存在している
- Aさんの2レコードだけがtrainに、残り2レコードはvalidationに割り振られたら、、、その2レコードについては確実に正解してしまう  
  &rarr; 「ここ進研ゼミで出たところ！」状態  

そうならないように同じグループのものはtrainならtrain、validationならvalidationに固める。

![figure GroupKFold](./figures/GroupKFold.png)
<div style="text-align: right;">
出典：https://scikit-learn.org/stable/auto_examples/model_selection/plot_cv_indices.html
</div>