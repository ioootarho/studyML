# studyML

https://suchu3.github.io/studyML/ のリポジトリ

## ハンズオン実行環境準備

DockerコンテナもしくはGoogle Colaboratory にて、ハンズオンのノートブック実行環境を準備します。

### Dockerコンテナを使用する場合

Docker Desktop がインストールされていることを前提とします。
また、動作確認は以下の環境にて行っています。
- Windows 10
    - Docker version 20.10.10
- macOS Catalina
    - Docker version 20.10.11

#### リポジトリをクローン

ここではデスクトップにクローンを作成するとします。
```
cd ~/Desktop
git clone https://github.com/suchu3/studyML.git
```

#### イメージをビルド

まず、リポジトリ中のdockerディレクトリに移動します。
```
cd ~/Desktop/studyML/docker
```

その状態でビルドコマンドを実行します。
```
docker build -t studyml_image .
```

#### コンテナを起動

イメージのビルド後、次のコマンドでイメージからコンテナを起動します。
```
docker run --name studyml_container -itd -v ~/Desktop/studyML/handson:/handson -p 3000:8888 studyml_image
```

#### Jupyter Lab の起動

次のコマンドで起動したコンテナの中に入ります。
```
docker exec -it studyml_container bash
```

その状態でJupyter Lab の起動コマンドを実行します。
```
jupyter-lab --ip 0.0.0.0 --allow-root
```

次のような表示が出れば起動成功です。  
![](./images/jupyterlab001.png)
赤枠内の?token=以降の文字列をコピーしておきます。

#### ブラウザ表示

chromeの新しいタブを開いて、アドレスバーに以下を入力します。
```
http://localhost:3000
```

次のような画面が表示されるので、コピーしておいた?token=以降の文字列を入力します。  
![](./images/jupyterlab002.png)

次のような画面に切り替われば成功です。  
画面左のディレクトリ一覧からhandsonをダブルクリックします。  
![](./images/jupyterlab003.png)  

その先でsrcディレクトリをダブルクリックします。  
![](./images/jupyterlab004.png)  

ハンズオン用のノートブックをダブルクリックして開きます。
![](./images/jupyterlab005.png)  

以上でDockerコンテナを使用したハンズオン環境準備は完了です。  

#### 終了時
Jupyter Lab を終了するときはターミナル上でCtrl+C を押下します。  
本当に終了するか確認されるのでy を入力してEnter を押下します。  

コンテナから抜けるときは次のコマンドを入力します。
```
exit
```

コンテナを停止させるときは次のコマンドを入力します。
```
docker stop studyml_container
```

コンテナを削除するときは次のコマンドを入力します。
```
docker rm studyml_container
```

ビルド済みのイメージを削除するときは次のコマンドを入力します。
```
docker rmi studyml_image
```