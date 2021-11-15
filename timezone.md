# timezone

- タイムゾーンの設定手順

## タイムゾーンを確認

- 設定可能なタイムゾーンを確認します

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

## タイムゾーンの設定

- 設定例では `JST` を設定しています

```
% sudo timedatectl set-timezone Asia/Tokyo
```