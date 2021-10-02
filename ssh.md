# ssh

- SSH server / client の設定手順

## 1. SSH server

### 1) サービス設定

- `/etc/ssh/ssd_config`の設定を変更します
- コメント部分は省略します
- 設定変更後サービスを再起動します

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

### 2) 公開鍵設定

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

## 2. SSH client

- SSHログイン用秘密鍵をクライアント環境へ配置します(USBメモリから配置します)

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