# Ubuntu 20.04 LTS

* Ubuntu Server 20.04 LTSをMacBook Air (2015)へインストールします。

## インストール

### インストールメディアの作成

* インストールイメージを[公式サイト]("https://jp.ubuntu.com/download/thank-you?version=20.04.3&architecture=amd64&platform=live-server")からダウンロードします。
* インストールイメージをUSBメモリへ書き込みます。

```
%> sudo diskutil unmountdisk /dev/disk2
%> dd if=/Users/hoge/Downloads/ubuntu-20.04.3-live-server-amd64.iso of=/dev/disk2
```

!!! note
    * 上記は、macOSでの作業例です。
    * USBメモリが`/dev/disk2`として認識されていることとします。


### インストール

- USBメモリをMacBook Airへ接続後、`option`キーを押しながらMacBook Airを起動します

#### 1) 言語を選択

- `English`を選択します (日本語は用意されていません)

#### 2) キーボードの選択

- `Japanese`を選択します (搭載されているキーボードに合わせて選択します)

#### 3) ネットワーク設定

- 何も設定せずに次へ進みます (この段階では内蔵WiFiは使用できません)

#### 4) Proxy設定

- 何も設定せずに次へ進みます

#### 5) Archive mirror設定

- デフォルト設定のまま次へ進みます

#### 6) ストレージ設定

##### ◾️ LVM (Logical Volume Manager)を使用する場合

- デフォルト設定のまま次へ進みます

##### ◾️ EXT4を使用する場合

- `Set up this disk as an LVM group` のチェックを外して次へ進みます

#### 7) 確認

- インストールする内容を確認して `Done` でインストールを開始します
- `Confirm destructive action`で最終確認のメッセージが表示されますのでインストールを開始する場合は`Continue`を選択します

#### 8) ユーザー情報設定

- インストールが完了するとユーザー情報の入力を求められますので下記の情報を入力して次へ進みます

|項目|情報|
|--:|:---|
|Your Name:|氏名|
|Your server's name:|コンピューター名|
|Pick a username:|ログインユーザー名|
|Choose a password:|ログインパスワード|
|Confirm your password:|ログインパスワード(確認用)|


#### 9) SSH設定

- SSH Serverをインストールする場合は `Install OpenSSH server` へチェックを入れて次へ進みます

#### 10) その他機能

- デフォルト設定のまま次へ進みます

#### 11) 再起動

- `Reboot` を選択してコンピューターを再起動します

## ネットワーク設定

* MacBook Air (2015)に搭載されている`Broadcom BMC4360`を使用します。

### ドライバーとユーティリティーのインストール

* `broadcom-sta-dkms`をダウンロードしてインストールします。

#### ダウンロード

* ネットワーク接続可能なUbuntu環境(WSLでも可)から、`apt-get`コマンドを使用してダウンロードします。

```
% sudo apt update
% sudo apt-get install -d broadcom-sta-dkms
% sudo apt-get install -d wpasupplicant
% sudo cd /var/cache/apt
% sudo tar zcvf /tmp/apt-archives.tar.gz ./archives
% cd /var/lib/apt
$ sudo tar zcvf /tmp/apt-lists.tar.gz ./lists
```

!!! note
    ネットワーク接続可能なUbuntu環境がない場合は、[パッケージ検索サイト]("https://packages.ubuntu.com")から依存関係も含めて手動でダウンロードすることも可能です。

#### インストール

* USBメモリからパッケージリストとパッケージ一式を配置します。
* インストール後にコンピューターを再起動します。

```
% cd /var/lib/apt
% sudo tar zxvf /media/apt-lists.tar.gz
% cd /var/cache/apt
% sudo tar zxvf /media/apt-archives.tar.gz
% sudo apt install broadcom-sta-dkms
% sudo apt install wpasupplicant
% shutdown -r now
```

### 設定

#### デバイス名を確認

* WiFiのデバイス名を確認します
* `lo` ではない方がWiFiのデバイス名になります(ここでは `wlp3s0` と認識されていることとします)

```
% ip a
1: lo: <LOOPBACK,,,>
  :
  :
2: wlp3s0: <BROADCAST, MULTICAST,,,>
  :
  :
```


#### 動作確認

* WiFiアダプタの動作確認をします
* WiFiアダプタを起動してアクセスポイントをスキャンします (近隣のSSIDがリストアップされることを確認します)

```
% sudo ip link set wlp3s0 up
% sudo iw wlp3s0 scan | grep SSID
```

#### 接続テスト

* `/etc/wpa_supplicant.conf`を作成し`wpa_supplicant`コマンドでアクセスポイントへ接続できることを確認します
* `wpa_supplicant`実行後に、アクセスポイントなどのIPアドレスへ向けて`ping`コマンドを実行しネットワーク接続できていることを確認します
* `ssid` と `psk` は使用しているアクセスポイントの設定を記述します

```
% sudo vim /etc/wpa_supplicant
---------------------------------------------------------------------------------------------------
network={
  ssid="WiFiAccessPointName"   <-- SSID名
  key_mgmt=WPA-PSK
  psk="wifipassword"    <-- パスワード
}
---------------------------------------------------------------------------------------------------

% sudo wpa_supplicant -B -c /etc/wpa_supplicant.conf -i wlp3s0
% ping 172.16.1.254
```

#### netplan設定

* コンピューター起動時からWiFi接続できるように`netplan`の設定を追加します
* IPアドレスとアクセスポイントの設定は使用している環境に合わせて記述します

```
% sudo vim /etc/netplan/99_wifi.yaml
---------------------------------------------------------------------------------------------------
network:
    version: 2
    renderer: networkd
    wifis:
        wlp3s0:
            dhcp4: false
            dhcp6: false
            optional: true
            addresses: [172.16.1.10/24]    <-- 固定IPアドレスを設定する場合に指定
            gateway4: 172.16.1.254 
            nameservers:
                addresses: [172.16.1.254]
            access-points:
                WiFiAccessPointName:　　　　　　<-- SSID名を記載 (""で括らない)
                    auth:
                        key-management: psk
                        password: "wifipassword"  <-- パスワード
---------------------------------------------------------------------------------------------------

% sudo netplan generate   <-- netplanの設定を作成
% sudo netplan apply      <-- netplanの設定を反映
% shutdown -r now
```

## OS・アプリケーションの更新

### パッケージリストの更新

* 公開されているパッケージのリストを更新します
* パッケージのインストール、アップグレードを行う前に実行します

```
% apt update
```

### パッケージのアップグレード

- パッケージを一括更新します

```
$ sudo apt upgrade
```

!!! note
    更新可能なパッケージリストは、`apt list --upgradable`で確認できます。

---

### パッケージの管理

#### パッケージの検索

* インストールしたいパッケージを検索します
* 検索する際に指定する `パッケージ名` は部分文字列でも指定可能です

```
$ apt search [パッケージ名]
```

#### パッケージのインストール

* パッケージをインストールします
* 依存パッケージも一緒にインストールされます

```
$ sudo apt install [パッケージ名]
```

#### パッケージのアンインストール

```
$ sudo apt remove [パッケージ名]
```

!!! note
    依存されていないパッケージングを一括削除する場合は、`sudo apt autoremove`を実行します。

## 時間設定

### タイムゾーンの設定

#### タイムゾーンを確認

* 設定可能なタイムゾーンを確認します

```
$ timedatectl list-timezones
・・・
・・・
・・・
Asia/Tokyo
・・・
・・・
・・・
```

#### タイムゾーンを設定

* 設定例では `JST` を設定しています

```
% sudo timedatectl set-timezone Asia/Tokyo
```

### 時刻同期設定

* 時刻同期(NTPクライアント)を設定します

#### 事前確認

* Ubuntu では `systemd-timesyncd` サービスを使用しますのでサービスが起動していることを確認します

```
% systemctl status systemd-timesyncd
```

#### 設定

* `/etc/systemd/timesyncd.conf` へ同期先のNTPサーバーのアドレスを設定します
* 設定例では [独立行政法人情報通信研究機構(NICT)](http://jjy.nict.go.jp/ntp/) の公開NTPサーバーを指定しています

```
% sudo vim /etc/systemd/timesyncd.conf
---------------------------------------------------------------------------------------------------
NTP=ntp.nict.jp
---------------------------------------------------------------------------------------------------
```

#### サービス再起動

- `systemd-timesyncd` サービスを再起動します

```
$ sudo systemctl restart systemd-timesyncd
```

## 日本語設定

* 日本語環境の設定手順

### 日本語関連パッケージのインストール

```
$ sudo apt install language-pack-ja-base language-pack-ja
```

### 言語設定できるロケールの確認

* `ja_JP.utf8`が追加されたことを確認します

```
$ locale -a
C
C.UTF-8
POSIX
en_US.utf8
ja_JP.utf8
```

### 言語設定

#### システム全体で設定する場合

```
$ sudo localectl set-locale LANG=ja_JP.UTF-8 LANGUAGE="ja_JP:ja"
$ sudo source /etc/default/locale
```

#### 各ユーザーアカウントで設定する場合

- ログインシェルスクリプトへ下記の設定を追加します
- 使用しているシェルに合わせて設定を追加します

```
$ echo 'export LANG=ja_JP.UTF-8' >> ~/.bashrc
$ echo 'export LANGUAGE="ja_JP:ja"' >> ~/.bashrc
$ source ~/.bashrc
```

### 確認

- `Current password:` へは何も入力せずにリターンで終了します

```
$ echo $LANG
ja_JP.UTF-8
$ passwd
xxxxx 用にパスワードを変更中
Current password: 
```

## SSH設定

* SSH server / client の設定手順

### SSH server

#### サービス設定

* `/etc/ssh/ssd_config`の設定を変更します
* コメント部分は省略します
* 設定変更後サービスを再起動します

```
% sudo vim /etc/ssh/sshd_config
---------------------------------------------------------------------------------------------------
Port 22
PermitRootLogin no
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys
PasswordAuthentication no
PermitEmptyPasswords no
ChallengeResponseAuthentication no
UsePAM yes
X11Forwarding yes
PrintMotd no
UseDNS no
AcceptEnv LANG LC_*
Subsystem sftp /usr/lib/openssh/sftp-server
---------------------------------------------------------------------------------------------------

% sudo systemctl stop ssh
% sudo systemctl start ssh
```

#### 公開鍵設定

- ログインユーザー毎に公開鍵を設定します
- 公開鍵とペアで作成される秘密鍵をSSHログイン元へコピーします

```
% ssh-keygen -t ed25519 -C hoge@ubuntu
Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/hoge/.ssh/id_ed25519):     <-- 公開鍵と秘密鍵のファイル名や作成先を変更する場合は入力
Enter passphrase (empty for no passphrase): 　　　　　　　　　　　　　　　<-- SSH接続する際のパスフレーズを入力
Enter same passphrase again:                                          <-- 同じパスフレーズを再入力

% cd .ssh
% cat id_ed25519.pub >> authorized_keys
% rm id_ed25519.pub
```

### SSH client

* SSHログイン用秘密鍵をクライアント環境へ配置します(USBメモリから配置します)

```
% cp /media/id_ed25519 ~/.ssh/
% vim ~/.ssh/config
---------------------------------------------------------------------------------------------------
host ubuntu
user hoge
hostname 172.16.1.10
port 22
identityfile ~/.ssh/id_ed25519
---------------------------------------------------------------------------------------------------
```

## rsyncサーバー

* rsync サーバーのセットアップ手順

### rsyncサービスの有効化

```
$ sudo vim /etc/default/rsync
---------------------------------------------------------------------------------------------------
RSYNC_ENABLE=true          # false --> true
---------------------------------------------------------------------------------------------------
```

### rsyncd設定ファイルの作成

```
$ sudo vim /etc/rsyncd.conf
---------------------------------------------------------------------------------------------------
#==================================================================================================
# Global options
#==================================================================================================
hosts allow        = localhost 192.168.11.0/24
hosts deny         = *
uid                = root
gid                = root
log file           = /var/log/rsyncd.log
pid file           = /var/run/rsyncd.pid
dont compress      = *.gz *.tgz *.zip *.pdf *.cab *.bz2 *.jpg *.gif *.png *.jpeg

#==================================================================================================
# Module options
#==================================================================================================
[backups]
    path           = /usr/local/backups
    comment        = backups
    use chroot     = true
    read only      = false
    incoming chmod = Du+rwx,g+rx,o+rx,Fa-x   # D=755 F=644
    outgoing chmod = Du+rwx,g+rx,o+rx,Fa-x   # D=755 F=644
---------------------------------------------------------------------------------------------------
```

### rsyncサービスの自動起動設定

```
$ sudo systemctl enable rsync.service
$ sudo systemctl list-unit-files | grep rsync
rsync.service                              enabled
```

### rsyncサービスの開始

```
$ sudo systemctl start rsync
```

### モジュールの確認方法

```
$ rsync -avvvn rsync://hoge.hoge.com .
opening tcp connection to hoge.hoge.com port 873
opening connection using --server --sender -vvvnlogDtpr .
backups         backups module
```

## ZSH

### インストール

```
$ sudo apt install zsh
```

### 設定

* `~/.zprofile`と`~/.zshrc`を作成します。

***~/.zprofile***
```sh
# path
typeset -U path
path=($path /sbin(N-/) bin(N-/) /usr/sbin(N-/) /usr/*/bin(N-/) /usr/local/bin(N-/) /usr/local/*/bin(N-/))

# EDITOR
export EDITOR=vim
```

### ログインシェルの変更

```
$ chsh -s /usr/bin/zsh
```

## Apache2

* apache2のセットアップ手順

### インストール

```
$ sudo apt install apache2
```

### 基本設定

#### セキュリティ設定

```
$ sudo vim /etc/apache2/conf-enabled/security.conf
---------------------------------------------------------------------------------------------------
ServerTokens Prod        # OS -> Prod
ServerSignature Off      # On -> Off
---------------------------------------------------------------------------------------------------
```

#### サーバー名の設定

```
$ sudo vim /etc/apache2/apache2.conf
---------------------------------------------------------------------------------------------------
ServerName web.hoge.com
---------------------------------------------------------------------------------------------------
```

####　管理者アドレスの設定

```
$ sudo vim /etc/apache2/sites-enabled/000-default.conf
---------------------------------------------------------------------------------------------------
<VirtualHost *:80>
    ServerAdmin webmaster@hoge.com     # webmaster@localhost -> webmaster@hoge.com
    ・・・
</VirtualHost>
---------------------------------------------------------------------------------------------------
```

### CGI設定


#### CGIモジュールの有効化

- `cgid` モジュールを有効化します
- モジュールの有効化後、`apache2` サービスを再起動します

```
$ sudo a2enmod cgid
Enabling module cgid.
To activate the new configuration, you need to run:
  systemctl restart apache2
$ sudo systemctl restart apache2
$
```

#### 任意CGIの設定

* 任意のディレクトリへ配置するCGIを設定します

```
$ sudo vim /etc/apache2/conf-available/cgi-script.conf
---------------------------------------------------------------------------------------------------
<IfModule mod_alias.c>
    <IfModule mod_cgi.c>
        Define ENABLE_CGI_SCRIPT
    </IfModule>

    <IfModule mod_cgid.c>
        Define ENABLE_CGI_SCRIPT
    </IfModule>

    <IfDefine ENABLE_CGI_BIN>
        ScriptAlias /cgi-script/ /var/www/cgi-script/
        <Directory "/var/www/cgi-script">
            Options +ExecCGI -MultiViews +SymLinksIfOwnerMatch
            AllowOverride None
            Require all granted
        </Directory>
    </IfDefine>
</IfModule>
---------------------------------------------------------------------------------------------------
```

#### 設定の有効化

- 設定を有効化後、`apache2` サービスを再起動します

```
$ sudo a2enconf cgi-script
Enabling conf cgi-script.
To activate the new configuration, you need to run:
  systemctl reload apache2
$ sudo systemctl restart apache2
$
```

#### CGIの配置

```
$ sudo vim /var/www/cgi-script/index.cgi
---------------------------------------------------------------------------------------------------
#!/usr/local/bin/python3
print('content-type: text/html\n')
print('<html>')
print('<body>')
print('It works in CGI')
print('</body>')
print('</html>')
---------------------------------------------------------------------------------------------------
$
$ sudo chmod 755 /var/www/cgi-script/index.cgi
$ sudo chown www-data:www-data /var/www/cgi-script/index.cgi
```

### サービスの再起動

```
$ sudo systemctl restart apache2
```

### 接続確認

#### デフォルトサイト

* Webブラウザから `http://web.hoge.com/` へアクセスしてデフォルトサイトが表示されることを確認します

#### CGIサイト

* Webブラウザから `http://web.hoge.com/cgi-script/` へアクセスしてサイトが表示されることを確認します

## Docker

* docker 環境のセットアップ手順
* docker 公式のリポジトリを使用します

### 準備

#### docker 公式GPG鍵のインストール

```
$ wget -qO- https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o ./docker.gpg
$ sudo install -o root -g root -m 644 docker.gpg /etc/apt/keyrings/
```

!!! warning
    サードパーティ製のGPG鍵を`/etc/apt/trusted.gpg.d`で管理することは**非推奨**となっています。
    

#### docker 公式リポジトリの追加

```
$ sudo sh -c 'echo "deb [arch=amd64 signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" > /etc/apt/sources.list.d/docker.list'
```

#### パッケージリストの更新

```
$ sudo apt update
```

### docker engin のインストール

* docker 公式の docker engin をインストールします

```
$ sudo apt install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

### docker グループへユーザーの追加

* docker グループへユーザーを追加することで `sudo` をせずに `docker` コマンドが使用できます
* docker グループへユーザーを追加後、再ログインする必要があります
* Debian系ディストリビューションでは `adduser -a ${USER} docker` でも追加できます

```
$ sudo gpasswd -a ${USER} docker
```

### 使用方法

#### イメージの操作

##### ◾️ 一覧表示

```
$ docker images
REPOSITORY   TAG     IMAGE ID       CREATED       SIZE
ubuntu       20.04   ba6acccedd29   7 weeks ago   72.8MB
```

##### ◾️ ダウンロード

* `docker pull REPOSITORY:TAG` でダウンロードします
* `TAG` を省略した場合は、最新TAGのイメージがダウンロードされます

```
$ docker pull ubuntu:20.04
```

##### ◾️ エクスポート

* `docker save REPOSITORY:TAG > image file name` でエクスポートします

```
$ docker save ubuntu:20.04 > ubuntu2004.img
```

##### ◾️ インポート

* `docker load < image file name` でインポートします
* インポートしたイメージは、`REPOSITORY` と `TAG` が `<none>` に設定されます
* `docker tag <IMAGE ID> REPOSITORY:TAG` で `REPOSITORY` と `TAG` を設定します

```
$ docker load < ubuntu2004.img
$ docker tag ba6acccedd29 ubuntu:20.04
```

##### ◾️ 削除

* `docker rmi REPOSITORY:TAG`、または `docker rmi <IMAGE ID>` で削除します

```
$ docker rmi ubuntu:20.04
```


#### コンテナの操作

##### ◾️ 一覧表示

- `-a` を指定しない場合は、実行中のコンテナのみが一覧表示されます

```
$ docker ps -a
CONTAINER ID   IMAGE          COMMAND   CREATED          STATUS                     PORTS   NAMES
076516f886a7   ubuntu:20.04   "bash"    36 seconds ago   Exited (0) 8 seconds ago           ubuntu-2004
```

##### ◾️ 作成

- `docker run [option] --name [container name] REPOSITORY:TAG` で作成します

```
$ docker run -it --name ubuntu-2004 ubuntu:20.04
```

##### ◾️ 起動

* `docker start NAME` または `docker start CONTAINER ID` で起動します

```
$ docker start ubuntu-2004
```

##### ◾️ 接続

* `docker attach NAME` または `docker attach CONTAINER ID` で起動しているコンテナに接続します

```
$ docker attach ubuntu-2004
root@076516f886a7:/# 
```

##### ◾️ 切断

* 切断した時点でコンテナも停止します

```
root@076516f886a7:/# exit
$
```

##### ◾️ 停止

* `docker stop NAME` または `docker stop CONTAINER ID` で停止します

```
$ docker stop ubuntu-2004
```

##### ◾️ 削除

* `docker rm NAME` または `docker rm CONTAINER ID` で削除します
* コンテナが停止している状態で削除します

```
$ docker rm ubuntu-2004
```

