# docker setup

- docker 環境のセットアップ手順
- docker 公式のリポジトリを使用します

## 1. 準備

### 1) docker 公式GPG鍵のインストール

```
$ wget -qO- https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o ./docker.gpg
$ sudo install -o root -g root -m 644 docker.gpg /etc/apt/trusted.gpg.d/
```

### 2) docker 公式リポジトリの追加

```
$ sudo sh -c 'echo "deb [arch=amd64 signed-by=/etc/apt/trustd.gpg.d/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" > /etc/apt/sources.list.d/docker.list'
```

### 3) パッケージリストの更新

```
$ sudo apt update
```

## 2. docker engin のインストール

- docker 公式の docker engin をインストールします

```
$ sudo apt install docker-ce
```

## 3. docker グループへユーザーの追加

- docker グループへユーザーを追加することで `sudo` をせずに `docker` コマンドが使用できます
- docker グループへユーザーを追加後、再ログインする必要があります
- Debian系ディストリビューションでは `adduser -a ${USER} docker` でも追加できます

```
$ sudo gpasswd -a ${USER} docker
```
