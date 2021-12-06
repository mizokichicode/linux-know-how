# docker

- docker の使い方

## image の操作

### 一覧表示

```
$ docker images
REPOSITORY   TAG     IMAGE ID       CREATED       SIZE
ubuntu       20.04   ba6acccedd29   7 weeks ago   72.8MB
```

### ダウンロード

- `docker pull REPOSITORY:TAG` でダウンロードします
- `TAG` を省略した場合は、最新TAGのイメージがダウンロードされます

```
$ docker pull ubuntu:20.04
```

### エクスポート

- `docker save REPOSITORY:TAG > image file name` でエクスポートします

```
$ docker save ubuntu:20.04 > ubuntu2004.img
```

### インポート

- `docker load < image file name` でインポートします
- インポートしたイメージは、`REPOSITORY` と `TAG` が `<none>` に設定されます
- `docker tag <IMAGE ID> REPOSITORY:TAG` で `REPOSITORY` と `TAG` を設定します

```
$ docker load < ubuntu2004.img
$ docker tag ba6acccedd29 ubuntu:20.04
```

### 削除

- `docker rmi REPOSITORY:TAG`、または `docker rmi <IMAGE ID>` で削除します

```
$ docker rmi ubuntu:20.04
```

---

## コンテナの操作

### 一覧表示

- `-a` を指定しない場合は、実行中のコンテナのみが一覧表示されます

```
$ docker ps -a
CONTAINER ID   IMAGE          COMMAND   CREATED          STATUS                     PORTS   NAMES
076516f886a7   ubuntu:20.04   "bash"    36 seconds ago   Exited (0) 8 seconds ago           ubuntu-2004
```

### 作成

- `docker run [option] --name [container name] REPOSITORY:TAG` で作成します

```
$ docker run -it --name ubuntu-2004 ubuntu:20.04
```

### 起動

- `docker start NAME` または `docker start CONTAINER ID` で起動します

```
$ docker start ubuntu-2004
```

### 接続

- `docker attach NAME` または `docker attach CONTAINER ID` で起動しているコンテナに接続します

```
$ docker attach ubuntu-2004
root@076516f886a7:/# 
```

### 切断

- 切断した時点でコンテナも停止します

```
root@076516f886a7:/# exit
$
```

### 停止

- `docker stop NAME` または `docker stop CONTAINER ID` で停止します

```
$ docker stop ubuntu-2004
```

### 削除

- `docker rm NAME` または `docker rm CONTAINER ID` で削除します
- コンテナが停止している状態で削除します

```
$ docker rm ubuntu-2004
```

