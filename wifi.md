# wifi

- MacBook Air (2015) 内蔵WiFi (Broadcom BMC4360) を使用した設定手順

## 1. 環境

- MacBook Air (2015)
- Broadcom BMC4360

## 2. ドライバーとユーティリティのインストール

- `broadcom-sta-dkms` をインストールします

### 1) 準備

- 下記の方法で `broadcom-sta-dkms` のパッケージをダウンロードします
  - ネットワーク接続可能な Ubuntu の環境から `apt-get` コマンドを使用してダウンロード
  - [パッケージ検索サイト]("https://packages.ubuntu.com")からダウンロード (依存関係も含めて手動でダウンロード)

### 2) ダウンロード

- `apt-get` コマンドでダウンロードする場合は下記の環境で作業が可能です
  - Ubuntu がインストールされたコンピューター
  - Windows 10 の WSL環境
- ダウンロードしたパッケージ一式とパッケージインデックスをアーカイブします
- アーカイブしたパッケージ一式とパッケージインデックスはUSBメモリでUbuntu Serverへコピーします

```
% sudo apt update
% sudo apt-get install -d broadcom-sta-dkms
% sudo apt-get install -d wpasupplicant
% cd /var/cashe/apt
% sudo tar zcvf /tmp/apt-archives.tgz ./archives
% cd /var/lib/apt
% sudo tar zcvf /tmp/apt-lists.tgz ./lists
```

### 3) インストール

- USBメモリーからパッケージ一式とパッケージインデックスを配置します (USBメモリは`/media`へマウントしたこととします)
- `apt`コマンドでインストールします
- ドライバーインストール後にコンピューターを再起動します

```
% sudo cp /media/apt-archives.tgz /var/cache/apt/
% sudo cp /media/apt-lists.tgz /var/lib/apt/
% cd /var/lib/apt
% sudo tar zxvf apt-lists.tgz
% sudo rm apt-lists.tga
% cd /var/cache/apt
% sudo tar zxvf apt-archives.tgz
% sudo rm apt-archives.tgz
% sudo apt install broadcom-sta-dkms
% sudo apt install wpasupplicant
% shutdown -r now
```

## 3. 設定

### 1) デバイス名を確認

- WiFiのデバイス名を確認します
- `lo` ではない方がWiFiのデバイス名になります(ここでは `wlp3s0` と認識されていることとします)

```
% ip a
1: lo: <LOOPBACK,,,>
  :
  :
2: wlp3s0: <BROADCAST, MULTICAST,,,>
  :
  :
```

### 2) 動作確認

- WiFiアダプタの動作確認をします
- WiFiアダプタを起動してアクセスポイントをスキャンします (近隣のSSIDがリストアップされることを確認します)

```
% sudo ip link set wlp3s0 up
% sudo iw wlp3s0 scan | grep SSID
```

### 3) 接続テスト

- `/etc/wpa_supplicant.conf`を作成し`wpa_supplicant`コマンドでアクセスポイントへ接続できることを確認します
- `wpa_supplicant`実行後に、アクセスポイントなどのIPアドレスへ向けて`ping`コマンドを実行しネットワーク接続できていることを確認します
- `ssid` と `psk` は使用しているアクセスポイントの設定を記述します

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

### 4) netplan設定

- コンピューター起動時からWiFi接続できるように`netplan`の設定を追加します
- IPアドレスとアクセスポイントの設定は使用している環境に合わせて記述します

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
