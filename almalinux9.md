# AlmaLinux 9

## 環境

* Windows 11 Pro/Home
* Windows Subsystem for Linux version 2 (WSL2)

## インストール

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

### AlmaLinux 9のインストール

* Microsoft Storeから`AlmaLinux 9`をインストールします。
* インストール後、ログインユーザー名とパスワードを設定します。

```
Enter new UNIX username: hoge    # ユーザー名を入力する
New password:                    # パスワードを入力する(8文字以上)
Retype new password:             # パスワードを再入力する
```

## 更新

* AlmaLinux 9を最新の状態へ更新します。

```
$ sudo dnf upgrade
```

## 初期設定

### systemdの有効化

* `systemd`を有効化します。

***/etc/wsl.conf***
```conf
[boot]
systemd=true
```

!!! warning
    `systemd`有効化設定後は、AlmaLinux 9を再起動してください。
    再起動は次のコマンドを実行します。
    ```
    > wsl.exe --terminate --distribution AlmaLinuxOS-9
    ```

### 日本語環境

* 日本語環境を設定します。

#### 日本語化パッケージのインストール

```
$ sudo dnf install langpacks-ja glibc-langpack-ja.x86_64
```