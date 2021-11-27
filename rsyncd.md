# rsyncd

- rsync サーバーのセットアップ手順

## 1. rsyncサービスの有効化

```
$ sudo vim /etc/default/rsync
---------------------------------------------------------------------------------------------------
RSYNC_ENABLE=true          # false --> true
---------------------------------------------------------------------------------------------------
```

## 2. rsyncd設定ファイルの作成

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

### 3. rsyncサービスの自動起動設定

```
$ sudo systemctl enable rsync.service
$ sudo systemctl list-unit-files | grep rsync
rsync.service                              enabled
```

### 4. rsyncサービスの開始

```
$ sudo systemctl start rsync
```

---

## モジュールの確認方法

```
$ rsync -avvvn rsync://hoge.hoge.com .
opening tcp connection to hoge.hoge.com port 873
opening connection using --server --sender -vvvnlogDtpr .
backups         backups module
```