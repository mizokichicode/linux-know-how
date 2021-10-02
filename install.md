# install

- MacBook Air (2015)へUbuntu Serverをインストールする
- macOSとは共存しない
- MacBook Air 内蔵の WiFi (Broadcom BCM4360) を使用してネットワーク接続する

## 1. 環境

- MacBook Air 2015
- Ubuntu Server 20.04.3 LTS

## 2. 準備

- [インストールイメージ]("https://jp.ubuntu.com/download/thank-you?version=20.04.3&architecture=amd64&platform=live-server")をダウンロードしてUSBメモリへ書き込む
- macOSでUSBメモリが `/dev/disk2` として認識されていることとします

```
% sudo diskutil unmountDisk /dev/disk2
% sudo dd if=/Users/hoge/Downloads/ubuntu-20.04.3-live-server-amd64.iso of=/dev/disk2
```

## 3. インストール

- USBメモリをMacBook Airへ接続後、`option`キーを押しながらMacBook Airを起動します

### 1) 言語を選択

- `English`を選択します (日本語は用意されていません)

### 2) キーボードの選択

- `Japanese`を選択します (搭載されているキーボードに合わせて選択します)

### 3) ネットワーク設定

- 何も設定せずに次へ進みます (この段階では内蔵WiFiは使用できません)

### 4) Proxy設定

- 何も設定せずに次へ進みます

### 5) Archive mirror設定

- デフォルト設定のまま次へ進みます

### 6) ストレージ設定

#### LVM (Logical Volume Manager)を使用する場合

- デフォルト設定のまま次へ進みます

#### EXT4を使用する場合

- `Set up this disk as an LVM group` のチェックを外して次へ進みます

### 7) 確認

- インストールする内容を確認して `Done` でインストールを開始します
- `Confirm destructive action`で最終確認のメッセージが表示されますのでインストールを開始する場合は`Continue`を選択します

### 8) ユーザー情報設定

- インストールが完了するとユーザー情報の入力を求められますので下記の情報を入力して次へ進みます

|項目|情報|
|--:|:---|
|Your Name:|氏名|
|Your server's name:|コンピューター名|
|Pick a username:|ログインユーザー名|
|Choose a password:|ログインパスワード|
|Confirm your password:|ログインパスワード(確認用)|


### 9) SSH設定

- SSH Serverをインストールする場合は `Install OpenSSH server` へチェックを入れて次へ進みます

### 10) その他機能

- デフォルト設定のまま次へ進みます

### 11) 再起動

- `Reboot` を選択してコンピューターを再起動します


