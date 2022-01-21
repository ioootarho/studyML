---
layout: default
title: 07, ツリー系アルゴリズム
nav_order: 8
math: mathjax3
---

# ツリー系アルゴリズム
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


ツリー系アルゴリズムとは決定木をベースにしたアルゴリズムの総称。  
ディープラーニングとは対照的に構造化データに強い。

## 決定木

決定木 (Decision Tree) とは、以下3種類のアルゴリズムの総称。  
- CART
- CHAID
- ID3 &rarr; C4.5 &rarr; C5.0  

いずれも次のような樹形図を描くので、決定木と呼ばれる。  
![Figure 34 DecisionTree](./figures/DecisionTree.png)  
<div style="text-align: right;">
出典：https://github.com/rasbt/python-machine-learning-book-2nd-edition/blob/master/code/ch03/images/03_21.png
</div>

3種類のアルゴリズムの比較は以下の通り。  
![Figure 35 DecisionTreeDIff](./figures/DecisionTreeDiff.png)  
<div style="text-align: right;">
出典：http://www.analyticsdlab.co.jp/column/decisiontree.html
</div>

- よく使われるのはCART
    - カテゴリ変数・連続変数を気にしなくて良い
    - 2分岐なので計算が速い

## アンサンブル

アンサンブル (Ensemble) とは複数のモデルを組み合わせる手法。  
- 弱学習器
    - 組み合わせる個々のモデル
    - アンサンブル素材
- 強学習器
    - 組み合わせた結果のモデル全体のこと
    - 最終的な出力を行う  

組み合わせ方によって、大きく以下5通りに分けられる。  
- Voting  (Hard Voting)  
    &rarr; 多数決をとる
    - 元のデータセットから複数の弱学習器を作る
    - 弱学習器の出力の多数決で最終的な出力を決める  
        &rarr; NERVのマギシステム
        &rarr; ぶっちゃけ実務では使わない
- Averaging  (Soft Voting)  
    &rarr; 平均をとる
    - 元のデータセットから複数の弱学習器を作る
    - 弱学習器の出力の平均を最終的な出力とする  
        &rarr; 弱学習器に多様性があった方が最終的な出力の精度は良くなる  
        &rarr; 三人寄れば文殊の知恵
        &rarr; めちゃくちゃよく使う
- Stacking  
    &rarr; 部下が上申して決裁者が意思決定する
    - 元のデータセットから複数の弱学習器を作る
    - 弱学習器の出力を特徴量として機械学習モデルを作る
    - その出力を最終的な出力とする  
        &rarr; うまく使えれば強力だが、強学習器を作るときに気をつけないと失敗する  
        - リーケージ
- Bagging (Bootstrap Aggrigating)  
    &rarr; サンプリングして大量にデータセット増やす
    - 元のデータセットから重複を許して復元抽出により大量のデータセットを作る
    - 各データセットにつき1つの弱学習器を作る
    - 弱学習器の出力を平均して最終的な出力とする  
        &rarr; ブートストラップ平均でバリアンス抑える効果がある  
        &rarr; 精度が上がるというよりは、汎化性能が上がる
- Boosting  
    &rarr; 間違えたところを重点的に復習する  
    - 復習の仕方で異なる2種類の実装方法
        - AdaBoost (Adaptive Boost)  
            - 分類問題でのみ使用可能
            - 元のデータセットの各サンプルに対して等しく重みを割り当てる
            - 1つ目の弱学習器を作る
            - 誤分類したサンプルの重みを増やし、正解したサンプルの重みを減らす
            - 2つ目の弱学習器を作る
            - 誤分類したサンプルの重みを増やし、正解したサンプルの重みを減らす
            - これを延々繰り返す
            - 各学習器の出力の重み付き和を最終的な出力とする
        - 勾配ブースティング (Gradient Boosting)
            - 元のデータセットから1つ目の弱学習器を作る
            - 1つ目の弱学習器の出力の残差を計算する
            - 元のデータセットの特徴量を用いて、1つ目の弱学習器の残差をターゲットにして2つ目の弱学習器を作る
            - 2つ目の弱学習器の残差を計算する
            - 元のデータセットの特徴量を用いて、2つ目の弱学習器の残差をターゲットにして3つ目の弱学習器を作る
            - 3つ目の弱学習器の残差を計算する
            - これを延々繰り返す
            - 各弱学習器の出力の重み付き和を最終的な出力とする  
    &rarr; 勾配ブースティングが頻出

### AdaBoost

Schapire (2013) によるAdaBoostのアルゴリズムは以下の通り。  

- Input
    - $n$組のサンプル$\{(x_1, y_1), \dots, (x_n, y_n)\}$
        - where $x_i \in \chi, \quad y_i \in \{-1, +1\}$
    - サンプル$i$の重み$D_1(i)$を$D_1(i)=\frac{1}{n}$で初期化
        - for $i=1, \dots, n$
- For $t=1, \dots, T$:
    - 重みベクトル$D_t$を用いて弱学習器を作成
        - $h_t:\chi \rightarrow \{-1, +1\}$
    - 弱学習器$h_t$の分類誤差を計算  
        - $\epsilon_t = \sum^{n}_{i=1}D_t(i)[h_t(x_i) \neq y_i]$  
            - $[h_t(x_i) \neq y_i]$は、$h_t(x_i) \neq y_i$のとき$1$を、$h_t(x_i) = y_i$のとき$0$を返す
    - $\alpha_t=\frac{1}{2}\ln (\frac{1-\epsilon_t}{\epsilon_t})$とおく
    - サンプル$i$の重み$D_{t+1}(i)$を$D_{t+1}(i)=\frac{D_t(i)\exp(-\alpha_t y_i h_t(x_i))}{Z_t}$で更新
        - for $i=1, \dots, n$
        - $Z_t$は基準化定数
- Final Output
    - $H(x) = sign(\sum^{T}_{t=1} \alpha_t h_t(x))$

## 主要なツリー系アルゴリズム

### ランダムフォレスト

https://www.slideshare.net/HitoshiHabe/ss-58784421

### 勾配ブースト木

### XGBoost

### LightGBM

### CatBoost