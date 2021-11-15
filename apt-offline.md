# apt-offline

- オフライン環境でのパッケージ管理
- インターネットに接続された Ubuntu の環境が1台は必要になります (WSL環境のUbuntuも可)

## 1. パッケージリストの更新

- ***オンライン環境*** で実行します

### 1) パッケージリストを更新

```
$ sudo apt update
```

### 2) パッケージリストのアーカイブを作成

```
$ cd /var/lib/apt/
$ sudo tar zcvf apt-lists.tgz lists
 ・・・
$
```

### 3) USBメディアへ保存

```
$ sudo mv apt-lists.tgz /media/
```


## 2. パッケージリストの展開

- ***オフライン環境*** で実行します

```
$ cd /var/lib/apt
$ sudo cp /media/apt-lists.tgz ./
$ sudo tar zxvf apt-lists.tgz
 ・・・
$ sudo rm apt-lists.tgz
```

## 3. 更新パッケージの確認

- ***オフライン環境*** で実行します

```
$ apt list --upgradable
```

## 4. パッケージのダウンロード

- ***オンライン環境*** で実行します

### 1) ダウンロード

```
$ sudo apt-get install --download-only [パッケージ名]
```

### 2) アーカイブの作成

```
$ cd /var/cache
$ sudo tar zcvf apt-cache-archives.tgz apt
 ・・・
$
```

### 3) USBメディアへコピー

```
$ sudo mv apt-cache-archives.tgz /media/
```

## 4. パッケージアーカイブの更新

- ***オフライン環境*** で実行します

```
$ cd /var/cache
$ sudo cp /media/apt-cache-archives.tgz ./
$ sudo tar zxvf apt-cache-archives.tgz
 ・・・
$ sudo rm apt-cache-archives.tgz
```

## 5. パッケージの更新

```
$ sudo apt upgrade
 ・・・
$
```

