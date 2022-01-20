---
layout: default
title: 04, 分類問題
nav_order: 5
math: mathjax3
---

# 分類問題
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


## 分類問題とは

分類問題とはYes/No, Positive/Negative のような2択を判別させる問題のこと。  
次のようにターゲットを数字に置き換えて問題を解く。  
- Positive &rarr; 1
- Negative &rarr; 0

治安の悪さと失業者数の例を分類問題として扱ってみる。  

前章で、刑法犯認知件数を$crime$ 完全失業者数を$unemp$として
\\[
\begin{align}
crime_i = \beta_0 + \beta_1unemp_i + \varepsilon \cr
\varepsilon \sim N(0, \sigma^2)
\end{align}
\\]
という線形回帰モデルを作成した。  
線形回帰モデルの作成は以下の流れで行った。  
- 仮説構築
    - 治安の悪さと失業者数には関係がある
    - 治安の悪さの代理変数として、刑法犯認知件数を用いる  
- 損失関数定義
- 損失関数を最小化するパラメータを求める
    - 最小二乗法
    - 勾配降下法
- 結果
    - 決定係数0.81の線形回帰モデルが出来上がった
- 結論
    - 刑法犯認知件数は完全失業者数で説明できる  
        &rarr; したがって、治安の悪さと失業者数には関係があると考えられる  
        ※ちゃんと分析するなら「市町村ごとの総人口の違いは考慮しなくて良いの？」というツッコミが入るが、ここではひとまず置いておく

ここで、「治安の悪さの代理変数として、刑法犯認知件数を用いる」という部分を掘り下げて、次のような仮説を考えてみる。  
- 新たな仮説
    - 刑法犯認知件数に閾値を定めて、それ以上なら治安が悪い、それ未満なら治安が良い、という2択にすれば、失業者数から治安の良し悪しをもっとはっきり判別することができるのではないか

仮に刑法犯認知件数の閾値を1,838市町村の平均数として、刑法犯認知件数がそれ以上の市町村は治安が悪い、それ未満の市町村は治安が良いと判断するフラグを立てる。
- 治安が悪い &rarr; 1  
- 治安が良い &rarr; 0  

![Figure 13 Crime Histogram](./figures/CrimeHistogram.png)  

新しく立てたこのフラグを$crime\_flag$とする。  
- $crime\_flag=1$  
    &rarr; 481市町村  
- $crime\_flag=0$  
    &rarr; 1,357市町村  

すると、相手にするのがこの散布図から  
![Figure 14 CrimeVsUnemployee](./figures/CrimeVsUnemployee.png)  
この散布図に変わる。  
![Figure 15 CrimeFlagVsUnemployee](./figures/CrimeFlagVsUnemployee.png)  

ここに今まで通り線形回帰を適用しても全然うまく当てはまらない。  
![Figure 16 ClassifiedByLinerRegressionModel](./figures/ClassifiedByLinerRegressionModel.png)  

さらに、試しに失業者数の上位5市町村を除いてみる。  
すると散布図はこうなる。  
![Figure 17 CrimeFlagVsUnemployeeExcluded](./figures/CrimeFlagVsUnemployeeExcluded.png)  

この状態で線形回帰を適用すると、決定境界の青い点線が簡単に左にズレる。  
![Figure 18 ClassifiedByLinerRegressionModelExcluded](./figures/ClassifiedByLinerRegressionModelExcluded.png)  

&rarr; 分類問題を解くためには、線形回帰ではない別の方法が必要。

## シグモイド曲線

分類問題を解くときに頻出するのがシグモイド曲線。  
シグモイド曲線とは必ず0~1の間に収まる魔法の曲線。  
![Figure 19 SigmoidFunction](./figures/SigmoidFunction.png)

この曲線を描く式は次の関数で定義される。  
\\[
\begin{align}
S(x) = \frac{1}{1 + e^{-x}} = \frac{e^x}{e^x + 1}
\end{align}
\\]
この関数をシグモイド関数と呼ぶ。  
&rarr; シグモイド関数に突っ込めば、どんな値でも0~1の間に収まる。

## ロジスティック回帰

乱暴な言い方をすれば、ロジスティック回帰とは線形回帰式の出力にシグモイド関数を噛ませて確率として扱えるようにしたモデル。
\\[
\begin{align}
y\in\{0, 1\} \cr
z = \beta_0 + \beta_1x \cr
P(Y=y) = \frac{1}{1+e^{-z}}
\end{align}
\\]

ただし、$y$は$0$か$1$の値をとる確率変数で、$Y$はその実現値とする。  
$P(Y=0)$は確率変数$y$が$0$をとる確率、$P(Y=1)$は確率変数$y$が$1$をとる確率を表す。  
そして、これらをまとめて確率変数$y$の関数として表記したものを確率関数と呼び、$p(y)=P(Y=y)$と表記する。  
&rarr; ロジスティック回帰は確率を扱える

- 理論面を掘り下げようとすると一般化線形モデルが出てくるが、時間がないのでその辺りはまた後日  
    - 一般化線形モデルについては久保 (2012) が定番の教科書
        - 通称「緑本」
- ロジスティック回帰を機械学習モデルとするか統計モデルとするかは主義・流派によって異なる
    - 線引きすること自体にあまり意味はない
- ロジスティック回帰自体はかなり古くからあるアルゴリズム
- では時代遅れのロートルか？  
    &rarr; No!  
    &rarr; 今でも通用する  
        - DataRobotはじめ各種AutoMLサービスが最初に走らせるアルゴリズムのひとつ
- ロジスティック回帰のすごいところ
    - シンプルなところ
        - シンプルゆえに計算が速い
        - シンプルゆえに解釈しやすい

このロジスティック回帰を用いて、分類問題を解いていく。  
作成するモデルは以下の通り。  

- 仮説
    - 治安が良いか悪いかを失業者の数から判別する
    - 治安が良ければ$y=0$、治安が悪ければ$y=1$として、完全失業者数を$unemp$とする
    - $P(Y=y)$がモデルから予測される確率になる
\\[
\begin{align}
y\in\{0, 1\} \cr
z = \beta_0 + \beta_1unemp \cr
P(Y=y) = \frac{1}{1+e^{-z}}
\end{align}
\\]

- ロジスティック回帰の損失関数
    - 分類問題なのでLog Loss
    - モデルから予測される確率$P(Y=y)$を$p$とおく
    - $n$をサンプルサイズとする
\\[
\begin{align}
J(\beta) = -\frac{1}{n} \sum_{i=1}^{n}\\{ y_i \log p_i + (1-y_i)\log (1-p_i)\\}
\end{align}
\\]

この損失関数を最小にするようなパラメータを勾配降下法で求める。