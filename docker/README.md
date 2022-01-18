- イメージビルド
```
docker build -t studyml_image .
```

- コンテナ起動
```
docker run --name studyml_container -itd -v /hoge/studyML/handson:/handson -p 3000:8888 studyml_image
```

- コンテナの中に入る
```
docker exec -it studyml_container bash
```

- Jupyter-Lab起動
```
jupyter-lab --ip 0.0.0.0 --allow-root
```