# japanese

- 日本語環境の設定手順

## 1. 日本語関連パッケージのインストール

```
$ sudo apt install language-pack-ja-base language-pack-ja
```

## 2. 言語設定できるロケールの確認

- `ja_JP.utf8`が追加されたことを確認します

```
$ locale -a
C
C.UTF-8
POSIX
en_US.utf8
ja_JP.utf8
```

## 3. 言語設定

### システム全体で設定する場合

```
$ sudo localectl set-locale LANG=ja_JP.UTF-8 LANGUAGE="ja_JP:ja"
$ sudo source /etc/default/locale
```

### 各ユーザーアカウントで設定する場合

- ログインシェルスクリプトへ下記の設定を追加します
- 使用しているシェルに合わせて設定を追加します

```
$ echo 'export LANG=ja_JP.UTF-8' >> ~/.bashrc
$ echo 'export LANGUAGE="ja_JP:ja"' >> ~/.bashrc
$ source ~/.bashrc
```

## 4. 確認

- `Current password:` へは何も入力せずにリターンで終了します

```
$ echo $LANG
ja_JP.UTF-8
$ passwd
xxxxx 用にパスワードを変更中
Current password: 
```