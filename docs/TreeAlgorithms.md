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
    - 元のデータセットから重複を許して復元抽出を行い大量のデータセットを作る
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
            - 回帰問題にも分類問題にも使用可能
            - 1つ目の弱学習器を作る
                - 回帰問題：ターゲットの平均値を出力
                - 分類問題：ターゲットの平均確率を出力
            - 1つ目の弱学習器の出力の残差を計算する
            - 元のデータセットの特徴量を用いて、1つ目の弱学習器の残差をターゲットに2つ目の弱学習器を作る
            - 2つ目の弱学習器の残差を計算する
            - 元のデータセットの特徴量を用いて、2つ目の弱学習器の残差をターゲットに3つ目の弱学習器を作る
            - 3つ目の弱学習器の残差を計算する
            - これを延々繰り返す
            - 各弱学習器の出力の重み付き和を最終的な出力とする  

### AdaBoost

Schapire (2013) によるAdaBoostのアルゴリズムは以下の通り。  

---
**Algorithm: AdaBoost**  

- Input:  
    - training examples $(x_1, y_1), \dots, (x_n, y_n)$ where $x_i \in \chi$, $y_i \in \\{-1, +1\\}$  
    - iteration number $T$
- Initialize:  
    - Suppose $D_1(i)$ as weight of sample $i$  
    - Initialize $D_1(i)=\frac{1}{n}$ for $i=1, \dots, n$  
- For $t=1, \dots, T$:  
    - Train weak learner using weight distribution $D_t$  
    - Get weak hypothesis $h_t:\chi \rightarrow \\{-1, +1\\}$  
    - Calculate error of $h_t$  
$$
\epsilon_t = \sum_{i=1}^{n}D_t(i)I(h_t(x_i) \neq y_i)
$$
        where $I(h_t(x_i) \neq y_i)$ is a indicator function:  
$$
I(h_t(x_i) \neq y_i)=
\begin{cases}
1 & h_t(x_i) \neq y_i \cr
0 & h_t(x_i) = y_i
\end{cases}
$$  
    - Set $\alpha_t=\frac{1}{2}\ln (\frac{1-\epsilon_t}{\epsilon_t})$  
    - Update, for $i=1, \dots, n$:  
\\[
D_{t+1}(i)=\frac{D_t(i)\exp(-\alpha_t y_i h_t(x_i))}{Z_t}
\\]
        where $Z_t$ is a normalization factor chosen so that $D_{t+1}$ will be a distribution  
- Output:  
    - Final hypothesis  
\\[
\begin{align}
H(x) = sign \left( \sum_{t=1}^{T} \alpha_t h_t(x) \right)
\end{align}
\\]  
        where $sign()$ is a sign function:  
\\[
sign(x)=
\begin{cases}
1 & x>0 \cr
0 & x=0 \cr
-1 & x<0
\end{cases}
\\]

---

### 勾配ブースティング

Friedman (2001) による勾配ブースティングのアルゴリズムは以下の通り。  

---
**Algorithm: Gradient Boosting for Regression**  

- Input:  
    - training set $(x_1, y_1), \dots, (x_n, y_n)$  
    - a differentiable loss function $L(y_i, F(x_i))$  
        - for regression, suquared error is commonly used as a loss function  
\\[
\begin{align}
L(y_i, F(x_i)) = \frac{1}{2}(y_i-F(x_i))^2
\end{align}
\\]
    - number of iterations $M$  
    - learning rate $\nu$  
- Initialize:  
    - Initialize model with a constant value  
\\[
\begin{align}
F_0(x) = \arg\min_{\gamma} \sum_{i=1}^{n}L(y_i, \gamma)
\end{align}
\\]
        where $\gamma$ refers to a predicted value  
    - In other words, initialize  
\\[
\begin{align}
F_0(x) = \frac{1}{n}\sum^{n}_{i=1}y_i
\end{align}
\\]
- For $m=1, \dots, M$:  
    - Compute so-called *pseudo-residuals*, for $i=1, \dots, n$:  
\\[
\begin{align}
r_{im} &= - \left[ 
    \frac{\partial L(y_i,F_{m-1}(x_i))}{\partial F_{m-1}(x_i)} 
    \right] \cr
&= - \left[
    \frac{\partial }{\partial F_{m-1}(x_i)} \frac{1}{2}(y_i-F_{m-1}(x_i))^2
    \right] \cr 
&= -(-y_i + F_{m-1}(x_i)) \cr
&= y_i - F_{m-1}(x_i)
\end{align}
\\]
    - Fit a weak learner to the *pseudo-residuals* $r_{im}$ and create terminal region $R_{jm}$ for $j=1, \dots, J_m$  
        - terminal region $R_{jm}$ is $j$-th leaf in iteration $m$  
        - $J_m$ means the total number of leaves in iteration $m$  
    - Compute, for $j=1, \dots, J_m$:  
\\[
\begin{align}
\gamma_{jm} = \arg\min_{\gamma} \sum_{x_i \in R_{ij}} L(y_i, F_{m-1}(x_i)+\gamma)
\end{align}
\\]
        where $\gamma$ refers to a predicted value  
    - Update model  
\\[
\begin{align}
F_m(x) = F_{m-1}(x) + \nu \sum_{j=1}^{J_m} \gamma_{jm} I(x \in R_{jm})
\end{align}
\\]
        where $I(x \in R_{jm})$ is a indicator function:  
\\[
I(x \in R_{jm}) =
\begin{cases}
1 & x \in R_{jm} \cr
0 & x \notin R_{jm}
\end{cases}
\\]

- Output:  
     - $F_M(x_i)$ as finally predicted value
---

勾配ブースティングを分類問題に用いる場合もアルゴリズムの枠組みは変わらない。  
最終出力の前にシグモイド関数を噛ませて、損失関数とその勾配の式が変わるだけ。  
    &rarr; 線形回帰とロジスティック回帰の違いと同様


---
**Algorithm: Gradient Boosting for Classification**  

- Input:  
    - training set $(x_1, y_1), \dots, (x_n, y_n)$  
    - a differentiable loss function $L(y_i, F(x_i))$  
        - for classification, Log Loss is commonly used as a loss function  
\\[
\begin{align}
Log Loss = -( y_i \log p_i + (1-y_i)\log (1-p_i) )
\end{align}
\\]
        - CAUTION: Log Loss is a function of observed $y_i$ and probability $p_i$. However, our weak learner $F(x_i)$ is expected to return real number.  
        - REMIND: Sigmoid function  
\\[
\begin{align}
S(z) = \frac{1}{1 + e^{-z}} = \frac{e^z}{e^z + 1} = p
\end{align}
\\]
        Sigmoid function is a function which converts real number $z$ to probability $p$.  
        The inverse function of sigmoid function is logit function defined as  
\\[
\begin{align}
logit(p) = \log \left( \frac{p}{1-p} \right) = \log(odds) = z
\end{align}
\\]
        since $\frac{p}{1-p}$ is called as odds, the logit is also called as log-odds.  
        - Using this formula, convert Log Loss to a function of observed $y_i$ and real number $\log(odds)$  
\\[
\begin{align}
Log Loss &= -( y_i \log p_i + (1-y_i)\log (1-p_i) ) \cr
&= -y_i \log p_i - (1-y_i)\log (1-p_i) \cr
&= -y_i \log p_i - \log (1-p_i) + y_i \log (1-p_i) \cr
&= -y_i( \log p_i - \log (1-p_i)) - \log(1-p_i) \cr
&= -y_i \log \left( \frac{p_i}{1-p_i} \right) - \log (1-p_i) \cr
&= -y_i \log(odds) - \log \left( 1- \frac{e^{\log(odds)}}{e^{\log(odds)}+1} \right) \cr
&= -y_i \log(odds) - \log \left( \frac{1}{e^{\log(odds)}+1} \right) \cr
&= -y_i \log(odds) - ( \log 1 - \log (e^{\log(odds)}+1) ) \cr
&= -y_i \log(odds) + \log (e^{\log(odds)}+1) \cr
\therefore L(y_i, F(x_i)) &= -y_i F(x_i) + \log (e^{F(x_i)}+1)
\end{align}
\\]
    - number of iterations $M$  
    - learning rate $\nu$  
- Initialize:  
    - Initialize model with a constant value  
\\[
\begin{align}
F_0(x) = \arg\min_{\gamma} \sum_{i=1}^{n}L(y_i, \gamma)
\end{align}
\\]
        where $\gamma$ refers to a predicted value  
    - In other words, initialize  
\\[
\begin{align}
F_0(x) = \log(odds \ of \ y) 
\end{align}
\\]
- For $m=1, \dots, M$:  
    - Compute so-called *pseudo-residuals*, for $i=1, \dots, n$:  
\\[
\begin{align}
r_{im} &= - \left[ 
    \frac{\partial L(y_i,F_{m-1}(x_i))}{\partial F_{m-1}(x_i)} 
    \right] \cr
&= - \left[
    \frac{\partial }{\partial F_{m-1}(x_i)} (-y_i F_{m-1}(x_i) + \log (e^{F_{m-1}(x_i)}+1))
    \right] \cr 
&= - \left( -y_i + \frac{e^{F_{m-1}(x_i)}}{e^{F_{m-1}(x_i)}+1} \right) \cr
&= \left( y_i - \frac{e^{F_{m-1}(x_i)}}{e^{F_{m-1}(x_i)}+1} \right) \cr
&= y_i - p_i
\end{align}
\\]
    - Fit a weak learner to the *pseudo-residuals* $r_{im}$ and create terminal region $R_{jm}$ for $j=1, \dots, J_m$  
        - terminal region $R_{jm}$ is $j$-th leaf in iteration $m$  
        - $J_m$ means the total number of leaves in iteration $m$  
    - Compute, for $j=1, \dots, J_m$:  
\\[
\begin{align}
\gamma_{jm} = \arg\min_{\gamma} \sum_{x_i \in R_{ij}} L(y_i, F_{m-1}(x_i)+\gamma)
\end{align}
\\]
        where $\gamma$ refers to a predicted value  
        - Approximate the loss function with a second order Tayler Series  
\\[
\begin{align}
L(y_i, F_{m-1}(x_i)+\gamma) \approx L(y_i, F_{m-1}(x_i)) + \frac{\partial L(y_i, F_{m-1}(x_i))}{\partial F_{m-1}(x_i)} \gamma + \frac{1}{2!} \frac{\partial^2 L(y_i, F_{m-1}(x_i))}{\partial F_{m-1}(x_i)^2} \gamma^2
\end{align}
\\]
        - Therefore, the derivative of the loss function with respect to $\gamma$ is  
\\[
\begin{align}
\frac{\partial}{\partial \gamma}L(y_i, F_{m-1}(x_i)+\gamma) \approx \frac{\partial L(y_i, F_{m-1}(x_i))}{\partial F_{m-1}(x_i)} + \frac{\partial^2 L(y_i, F_{m-1}(x_i))}{\partial F_{m-1}(x_i)^2} \gamma
\end{align}
\\]
        - Set this equal to $0$ and solve for $\gamma$  
\\[
\begin{align}
\frac{\partial L(y_i, F_{m-1}(x_i))}{\partial F_{m-1}(x_i)} &+ \frac{\partial^2 L(y_i, F_{m-1}(x_i))}{\partial F_{m-1}(x_i)^2} \gamma = 0 \cr
\frac{\partial L(y_i, F_{m-1}(x_i))}{\partial F_{m-1}(x_i)} &= - \frac{\partial^2 L(y_i, F_{m-1}(x_i))}{\partial F_{m-1}(x_i)^2} \gamma \cr
\gamma &= \frac{ - \frac{\partial L(y_i, F_{m-1}(x_i))}{\partial F_{m-1}(x_i)} }{ \frac{\partial^2 L(y_i, F_{m-1}(x_i))}{\partial F_{m-1}(x_i)^2} }
\end{align}
\\]
        - Caluculate the second derivative of the loss function  
\\[
\begin{align*}
\frac{\partial^2 L(y_i, F_{m-1}(x_i))}{\partial F_{m-1}(x_i)^2} &= \frac{\partial}{\partial F_{m-1}(x_i)} \left( \frac{\partial L(y_i, F_{m-1}(x_i))}{\partial F_{m-1}(x_i)} \right) \cr
&= \frac{\partial}{\partial F_{m-1}(x_i)} \left( -y_i + \frac{e^{F_{m-1}(x_i)}}{e^{F_{m-1}(x_i)}+1} \right)  \cr
&= \frac{\partial}{\partial F_{m-1}(x_i)} \left( -y_i + e^{F_{m-1}(x_i)} \cdot (e^{F_{m-1}(x_i)}+1)^{-1} \right) \cr
&= -1 \cdot (e^{F_{m-1}(x_i)}+1)^{-2} \cdot e^{F_{m-1}(x_i)} \cdot e^{F_{m-1}(x_i)} + e^{F_{m-1}(x_i)} \cdot (e^{F_{m-1}(x_i)}+1)^{-1} \cr
&= \frac{ - e^{2F_{m-1}(x_i)} }{ (e^{F_{m-1}(x_i)}+1)^{2} } + \frac{ e^{F_{m-1}(x_i)} }{ (e^{F_{m-1}(x_i)}+1) } \cr
&= \frac{ e^{F_{m-1}(x_i)} }{ (e^{F_{m-1}(x_i)}+1)^{2} } \cr
&= \frac{ e^{F_{m-1}(x_i)} }{ (e^{F_{m-1}(x_i)}+1) } \cdot \frac{1}{ (e^{F_{m-1}(x_i)}+1) } \cr
&= p_i(1-p_i)
\end{align*}
\\]
        - Therefore, optimal $\gamma_{jm}$ is  
\\[
\begin{align}
\gamma_{jm} &= \frac{ - \frac{\partial L(y_i, F_{m-1}(x_i))}{\partial F_{m-1}(x_i)} }{ \frac{\partial^2 L(y_i, F_{m-1}(x_i))}{\partial F_{m-1}(x_i)^2} } \cr
&= \frac{y_i-p_i}{p_i(1-p_i)}
\end{align}
\\]
    - Update model  
\\[
\begin{align}
F_m(x) = F_{m-1}(x) + \nu \sum_{j=1}^{J_m} \gamma_{jm} I(x \in R_{jm})
\end{align}
\\]
        where $I(x \in R_{jm})$ is a indicator function:  
\\[
I(x \in R_{jm}) =
\begin{cases}
1 & x \in R_{jm} \cr
0 & x \notin R_{jm}
\end{cases}
\\]

- Output:
    - Using $F_M(x_i)$, get probability  
\\[
\begin{align}
\frac{ e^{F_M(x_i)} }{ e^{F_M(x_i)} +1 }
\end{align}
\\]

---

## 主要なツリー系アルゴリズム

### ランダムフォレスト

ランダムフォレストとは次のようなアンサンブルモデル。

- 弱学習器に決定木を使用したBagging  
- ランダムに特徴量を非復元抽出してから各弱学習器を作る  
    - 通常のBaggingでは全特徴量を使用して各学習器を作る  
&rarr; 2つのランダム性を取り入れて汎化性能を高めている  
    - サンプルの復元抽出（行方向のランダム性）
    - 特徴量の非復元抽出（列方向のランダム性）

参考：https://www.slideshare.net/HitoshiHabe/ss-58784421

### 勾配ブースト決定木

勾配ブースト木 (Gradient Boosted Decision Tree; GBDT) とは次のようなアンサンブルモデル。

- 弱学習器に決定木を使用した勾配ブースティング  
&rarr; ランダムフォレストよりも精度は出やすいが、代わりに汎化性能が低くなりやすい  
- early stopping  
    - Boosting を一定回数繰り返しても精度改善が見られなくなったら学習を打ち止める  
    &rarr; 計算時間の短縮、消費メモリの抑制、過学習の防止

### XGBoost

XGBoost (eXtreme Gradient Boosting) とは次のようなアンサンブルモデル。

- GBDTがベース


### LightGBM

### CatBoost