# Dockerの導入
DokcerHubとDockerDesktopを導入するだけだが、備忘録として。
## DockerHubの登録
DockerHubに登録・ログインする。（アカウントを持っているのでログインしておく）
[dockerhub公式](https://hub.docker.com/)
DockerDesktopをダウンロードして、起動させると右上にdockerのアイコンが表示される。（dockerコマンドが有効になる。）<br />
一応以下のコマンドを実行し、Docker・docker-composeがインストールされているか確認する。
```
docker -v

// 実行結果
Docker version 20.10.22, build 3a2c30b
```
docker-composeも同時にインストールされているはず。
```
docker-compose -v

// 実行結果
Docker Compose version v2.15.1
```
