# docker setup (wsl)

- WSL2 での docker 環境の構築手順

## 1. WSL2 のセットアップ

- 詳細は [WSLのインストール](https://docs.microsoft.com/ja-jp/windows/wsl/install) を参照してください


### 1) ディストリビューションのインストール

```
> wsl --install -d Ubuntu-20.04
```

### 2) WSLデフォルトバージョンの設定

- WSLのデフォルトバージョンを `2` へ設定します

```
> wsl --set-default-version 2
```

#### デフォルトバージョンの設定に失敗する場合

1. `Windows Subsystem for Linux` の有効化
1. `Virtual Machine Platform` の有効化
1. [WSL2用Linuxカーネル](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi) のインストール

```
> dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
> dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all
```

### 3) ディストリビューションのWSLバージョンの設定

```
> wsl --set-version Ubuntu-20.04 2
```

## 2. docker engin のインストール

- インストール手順は [docker setup](./docker-setup.md) を参照してください

## 3. サービスの開始

```
$ sudo service start docker
```