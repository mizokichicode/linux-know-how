# python 3.x

- Python 3.x のインストール手順
- Python3.9.9のソースコードをビルドしてインストールする手順を説明します


## 1. ソースコードの取得

- [github](https://github.com/python/cpython)からソースコードを取得します

```
$ git clone --filter=blob:none https://github.com/python/cpython.git
・・・
$ cd cpython
$ git checkout 3.9
・・・
$ git branch 3.9.9 v3.9.9
$ git checkout 3.9.9
```

## 2. Makefileの作成

```
$ ./configure --enable-optimizations
```

## 3. ビルド

- `-j` オプションを指定することでビルドの高速化が可能です
- `-j` オプションで指定する値は `nproc` コマンドで確認できます

```
$ mak -j 4
```

## 4. インストール

- Ubuntu 20.04 にデフォルトインストールされている Python 3.8 と共存させます
- `/usr/local/bin` へインストールされます

```
$ sudo make altinstall
```

## 5. シンボリックリンクの作成

- インストールしたPython3.9.9をデフォルトで使用できるようにシンボリックリンクを作成します

```
$ sudo ln -s 2to3-3.9 2to3
$ sudo ln -s idle3.9 idle3
$ sudo ln -s pydoc3.9 pydoc3
$ sudo ln -s python3.9 python3
$ sudo ln -s python3.9-config python3-config
```