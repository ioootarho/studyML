# 推論サーバ構築手順

## はじめに

ハンズオン #7 モデルデプロイで使用する推論サーバのコンテナを起動する手順です。  
ここで使用するコンテナは以下リポジトリを参考にして作成しています。  
https://github.com/shibuiwilliam/ml-system-in-actions/tree/main/chapter4_serving_patterns/web_single_pattern

## 前提

- Python 3.8 以上
- Docker

## コンテナ起動

ここではデスクトップにリポジトリのクローンがあるとしています。

0. デプロイするONNXファイルを所定ディレクトリにコピー
既に同名ファイルが存在していた場合は上書きします。

- コピー元
```
~/Desktop/studyML/handson/src/lgb.onnx
```

- コピー先
```
~/Desktop/studyML/handson/deploy_handson/models/lgb.onnx
```

1. 推論サーバ構築作業ディレクトリへ移動

```sh
$ cd ~/Desktop/studyML/handson/deploy_handson
```

2. Docker イメージをビルド
makeコマンドが入っていない環境の場合は、コメントアウトされているdocker buildコマンドを実行してください。

```sh
$ make build_all
# 実行されるコマンド
# docker build \
#     -t studyml/deploy_handson:web_single_pattern_0.0.1 \
#     -f Dockerfile \
#     .
```

3. Docker でサービスを起動
makeコマンドが入っていない環境の場合は、コメントアウトされているdocker runコマンドを実行してください。

```sh
$ make run
# 実行されるコマンド
# docker run \
#     -d \
#     --net deploy-handson-network \
#     --name web_single_pattern \
#     -p 8000:8000 \
#     studyml/deploy_handson:web_single_pattern_0.0.1
```

## リクエスト実行

起動した API にローカルホストからリクエストを投げます。  
windowsのコマンドプロンプトから実行すると、推論リクエスト1, 2のコマンドが通らない場合があります。  
その場合はgit bashなどから実行してみてください。

```sh
# ヘルスチェック
$ curl localhost:8000/health
# 出力
# {
#   "health":"ok"
# }

# メタデータ
$ curl localhost:8000/metadata
# 出力
# {
#   "data_type": "float32",
#   "data_structure": "(1,19)",
#   "data_sample": [[1.0,0.0,1.0,1.0,-0.79199,1.0,1.0,3.0,3.0,1.0,3.0,1.0,3.0,1.0,1.0,1.0,3.0,0.04635515,-0.60534847]],
#   "prediction_type": "float32",
#   "prediction_structure": "(1,2)",
#   "prediction_sample": [0.6801156401634216,0.31988435983657837]
# }


# ラベル一覧
$ curl localhost:8000/label
# 出力
# {
#   "0": "not churn",
#   "1": "churn"
# }


# テストデータで推論リクエスト
$ curl localhost:8000/predict/test
# 出力
# {
#   "prediction": [0.6801156401634216,0.31988435983657837]
# }


# 推論リクエスト1
$ curl \
    -X POST \
    -H "Content-Type: application/json" \
    -d '{"data": [[2,0,2,1,1.0809139467873803,1,1,3,3,1,3,3,3,1,3,2,2,0.14103488739658718,0.7939244400352684]]}' \
    localhost:8000/predict
# 出力
# {
#   "prediction": [0.9455671310424805,0.05443286895751953]
#   ]
# }

# 推論リクエスト2
$ curl \
    -X POST \
    -H "Content-Type: application/json" \
    -d '{"data": [[2,0,2,1,-1.199142974743425,2,3,3,3,1,3,3,3,3,1,2,4,-1.0034271946515967,-0.9422390569253819]]}' \
    localhost:8000/predict
# 出力
# {
#   "prediction": [0.7722222208976746,0.22777777910232544]
#   ]
# }
```

## コンテナ停止

makeコマンドが入っていない環境の場合は、コメントアウトされているdocker rmコマンドを実行してください。

```sh
$ make stop
# 実行されるコマンド
# docker rm \
#   -f web_single_pattern
```
