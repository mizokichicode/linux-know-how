# Ubuntu 24.04 LTS

## 環境

* Windows 11 Pro/Home
* Windows Subsystem for Linux version 2 (WSL2)

## インストール

* Windows Subsystem for LinuxへUbuntu 24.04 LTSをインストールします。

### Windows Subsystem for Linuxのインストール

* `Windows Terminal`、または`WindowsPowerShell`から次のコマンドを実行します。
* インストール後、コンピューターの再起動を要求される場合は、指示にしたがいコンピューターを再起動します。

```
> wsl.exe --install --no-distribution --inbox
```

!!! note
    Windowsの機能の`Linux用Windowsサブシステム`が有効化されます。


### Windows Subsystem for Linuxの更新

* Windows Subsystem for Linuxのコンポーネント、Linuxカーネルを更新します。
* `Windows Terminal`、または`WindowsPowerShell`から次のコマンドを実行します。

```
> wsl.exe --update
```

!!! note
    Microsoft Store版のWindows Subsystem for Linuxへ更新されます。


### Ubuntu 24.04 LTSのインストール

* Ubuntu 24.04 LTSをインストールします。
* `Windows Terminal`、または`WindowsPowerShell`から次のコマンドを実行します。
* インストール後、Ubuntu 24.04へログインするユーザー名とパスワードを設定します。

```
> wsl.exe --install Ubuntu-24.04
 :
 :
Enter new UNIX username: hoge    # ユーザー名を入力する
New password:                    # パスワードを入力する
Retype new password:             # パスワードを再入力する
```

!!! note
    Windows Subsystem for Linuxで利用可能なディストリビューションの一覧は次のコマンドで確認します。

    ```
    > wsl.exe --list --online
    ```

## 更新

* Ubuntu 24.04 LTSを最新の状態へ更新します。

### パッケージリポジトリの更新

```
$ sudo apt update
```

!!! note
    更新パッケージの一覧は、次のコマンドで確認します。

    ```
    $ apt list --upgradable
    ```


### 更新パッケージのアップグレード

```
$ sudo apt upgrade
```

## 初期設定

### 日本語環境

* 日本語環境を設定します。

#### 日本語パッケージのインストール

```
$ sudo apt update
$ sudo apt install language-pack-ja
```

#### 言語設定

```
$ sudo localectl set-locale LANG=ja_JP.UTF-8 LANGUAGE="ja_JP:ja"
$ source /etc/default/locale
```

!!! note
    * ユーザーアカウント単位で言語設定を行う場合は、ログインスクリプトへ次の設定を追加します。
    * 使用するシェルの仕様に合わせて設定します。

    ```
    $ echo 'export LANG=ja_JP.UTF-8' >> ~/.bashrc
    $ echo 'export LANGUAGE="ja_JP:ja"' >> ~/.bashrc
    $ source ~/.bashrc
    ```

### vimの設定

* vim使用時の基本設定をします。

***~/.vimrc***
```
set encoding=utf-8
set fileencodings=utf-8,iso-2022-jp,sjis,euc-jp,ucs-bom
set fileformats=unix,dos
set number
set autoindent
set tabstop=4
set shiftwidth=4
syntax on
```

### SSHの設定

* SSHログイン用の秘密鍵を作成します。
* SSHログイン用の公開鍵はログイン先サーバーへ設定します。

#### 公開鍵と秘密鍵を作成

```
$ ssh-keygen -t ed25519 -C hoge@ubuntu
Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/hoge/.ssh/id_ed25519):    # 公開鍵/秘密鍵のファイル名や作成先を変更する場合は入力する
Enter passphrase (empty for no passphrase):                          # SSH接続時のパスフレーズを入力
Enter same passphrase again:                                         # SSH接続時のパスフレーズを再入力
```

#### 接続先サーバーの設定

***~/.ssh/config***
```
host ubuntu
user hoge
hostname ubuntu.local
port 22
identityfile ~/.ssh/id_ed25519
```

## Docker

* docker公式サイトから`docker community edition`をインストールします。

### インストール

#### 公式GPG鍵のインストール

```
$ wget -qO- https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o ./docker.gpg
$ sudo install -o root -g root -m 644 docker.gpg /etc/apt/trusted.gpg.d/
```

#### 公式リポジトリの追加

```
$ sudo sh -c 'echo "deb [arch=amd64 signed=/etc/apt/trusted.gpg.d/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" > /etc/apt/sources.list.d/docker.list'
```

#### docker community editionのインストール

```
$ sudo apt update
$ sudo apt install docker-ce docker-compose
```

### 設定

#### dockerを使用するユーザーアカウントをdockerグループへ追加

```
$ sudo gpasswd -a ${USER} docker
```

!!! note
    * dockerグループへユーザーアカウントを追加することでdockerコマンド実行時の `sudo` を回避できます。
    * dockerグループへのユーザーアカウント追加後は再ログインしてください。


## その他

### ZSH

#### インストール

```
$ sudo apt update
$ sudo apt install zsh
```

#### ログインシェルの変更

```
$ chsh -s /usr/bin/zsh
```

!!! warning
    * `~/.zprofile`と`~/.zshrc`はあらかじめ作成しておいてください。
