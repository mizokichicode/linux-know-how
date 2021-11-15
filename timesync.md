# timesync

- 時刻同期(NTPクライアント)を設定します

## 事前確認

- Ubuntu では `systemd-timesyncd` サービスを使用しますのでサービスが起動していることを確認します

```
% systemctl status systemd-timesyncd
```

## 設定

- `/etc/systemd/timesyncd.conf` へ同期先のNTPサーバーのアドレスを設定します
- 設定例では [独立行政法人情報通信研究機構(NICT)](http://jjy.nict.go.jp/ntp/) の公開NTPサーバーを指定しています

```
% sudo vim /etc/systemd/timesyncd.conf
---------------------------------------------------------------------------------------------------
NTP=ntp.nict.jp
---------------------------------------------------------------------------------------------------
```

## サービス再起動

- `systemd-timesyncd` サービスを再起動します

```
$ sudo systemctl restart systemd-timesyncd
```