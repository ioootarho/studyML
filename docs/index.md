---
layout: default
title: 00, はじめに
nav_order: 1
math: mathjax3
---

# はじめに
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


本資料は「機械学習とは何か？」という疑問からスタートし、機械学習に関する周辺知識の中でも実務に直結するものを中心に取り扱う。  
本資料のゴールは、ハンズオンを通して与えられたデータから機械学習モデルを作成・運用に乗せるまでの一連の流れを掴むこと。  

## 本資料で扱うこと

- イメージを掴むための日本語の説明
- 背景を理解するための最低限の数式
- pythonで実装したコード

## 本資料で扱わないこと

- pythonの文法説明
- 各種ライブラリの使い方

## ハンズオン実行環境準備

GitHubリポジトリの[ハンズオン実行環境準備](https://github.com/suchu3/studyML#studyml)手順を参照。  
以下2通りの環境を用意しているので、好みの方でハンズオン可能。

- Dockerコンテナを使った環境
- Google Colaboratory を使った環境

## 参考文献

- D. Poole, A. Mackworth and R. Goebel (1998) "Computational Intelligence: A Logical Approach", New York: Oxford University Press
- T. Mitchell (1997) "Machine Learning", New York: McGraw Hill
- ペドロ・ミンゴス著, 神嶌敏弘訳 (2021) "マスターアルゴリズム：世界を再構築する「究極の機械学習」", 講談社
- 松尾豊 (2015) "人工知能は人間を超えるか：ディープラーニングの先にあるもの", KADOKAWA
- John. R. Searle (1980) "Minds, brains, and programs", Behavioral and Brain Sciences, vol.3, (3), pp.417-457
- S. Russell and P. Norvig (2003) "Artificial Intelligence: A Modern Approach", Upper Saddle River, New Jersey: Prentice Hall
- 江崎貴裕 (2020) "データ分析のための数理モデル入門：本質をとらえた分析のために", ソシム
- 竹村彰通 (2020) "新装改訂版 現代数理統計学", 学術図書
- 加藤公一 (2018) "機械学習のエッセンス", SBクリエイティブ
- 久保拓弥 (2012) "データ解析のための統計モデリング入門：一般化線形モデル・階層ベイズモデル・MCMC", 岩波書店
- 荒木修・齋藤智彦 (2016) "本質から理解する 数学的手法", 裳華房
- Sebastian Raschka, Vahid Mirjalili著, 株式会社クイープ訳, 福島真太朗監訳 (2018) "[第2版] Python機械学習プログラミング：達人データサイエンティストによる理論と実践", インプレス
- K. He, X. Zhang, S. Ren and J. Sun (2015) "Deep Residual Learning for Image Recognition", arXiv:1512.03385
- R. E. Schapire (2013) "Explaining AdaBoost", Empirical Inference, Chapter 5, pp.37-52
- J. H. Friedman (2001) "Greedy Function Approximation: A Gradient Boosting Machine", Annals of Statistics, vol.29, (5), pp.1189-1232
- T. Chen and C. Guestrin (2016) "XGBoost: A Scalable Tree Boosting System", KDD'16, pp.785-794
- G. Ke, Q. Meng, T. Finely, T. Wang, W. Chen, W. Ma, Q. Ye and T. Y. Liu (2017) "LightGBM: A Highly Efficient Gradient Boosting Decision Tree", NIPS 2017
- L. Prokhorenkova, G. Gusev, A. Vorobev, A. V. Dorogush and A. Gulin (2017) "CatBoost: unbiased boosting with categorical features", 	arXiv:1706.09516
- J. Bergstra and Y. Bengjo (2012) "Random Search for hyper-parameter optimization", Journal of Machine Learning Research, vol.13, (2), pp.281-305
- 澁井雄介 (2021) "AIエンジニアのための機械学習システムデザインパターン", 翔泳社
- D. Scully, G. Holt, D. Golovin, E. Davydov, T. Phillips, D. Ebner, V. Chaudhary, M. Young, J. F. Crespo and D. Dennison (2015) "Hidden Technical Debt in Machine Learning Systems", Advances in Neural Information Processing Systems, vol.28
- J. Gama, I. Zliobaite, A. Bifet, M. Pechenizkiy and A. Bouchachia (2014) "A survey on concept drift adaption", Association for Computing Machinery, vol.46, (4), pp.1-37