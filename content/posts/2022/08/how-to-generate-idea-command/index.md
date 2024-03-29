---
title: "IntelliJ idea コマンドを使えるようにする方法 (The specified directory is not writable への対応)"
date: 2022-08-17T21:07:52+09:00
tags: ["Linux", "IntelliJ"]
---

コマンドラインに `idea .` と打つと IDE が開いて欲しかったが、デフォルトでは使えなかったためやり方をまとめた

# 環境
|  Name  |  Version  |
| ---- | ---- |
|  macOS Monterey(M1 Macbook Pro) |  12.1 |
|  JetBrains Toolbox App  |  1.25  |
|  IntelliJ IDEA (Ultimate Edition) |  2022.2 |


# 現象
まず見たのは[こちら](https://pleiades.io/help/idea/working-with-the-ide-features-from-command-line.html#toolbox)で、簡単そうだったので Toolbox を install した。

しかし公式の手順通り Toolbox 上で `/usr/local/bin` を Shell scripts location に設定しても `The specified directory is not writable` とエラーが表示された。

# 解決方法
結論から言うと書き込み権限の付与を行うことで解消された。

## 権限変更コマンド
```bash
sudo chmod 777 /usr/local/bin
```
権限付与後、`idea .` 実行エラーとなった場合は Toolbox の再起動が有効。

## 操作手順

作業した手順を記載しておく。初期値は以下のようになっていた。

```bash
ls -l /usr/local
drwxr-xr-x 19 root  wheel  608 Jul 13 00:27 bin/
```

root ユーザー以外の書き込み権限は確かにないため以下の通り権限を与える。

```bash
sudo chmod 777 /usr/local/bin
```

上記コマンドによって、以下が確認できた。

```bash
# 権限が以下のように更新されたこと
ls -l /usr/local
drwxrwxrwx  25 root  wheel  800 Aug 17 21:00 bin/

# idea ファイルが新たに生成されたこと
ls -l /usr/local/bin/
-rwxr-xr-x  1 sayakan  wheel       487 Aug 17 21:00 idea*
```

以上で終了だが、`/usr/local/bin/` の権限をこのままフルにしておくのは気が引けるので以下コマンドによって元の権限に戻しておいた。

```bash
sudo chmod 755 /usr/local/bin/
```


# References
- https://pleiades.io/help/idea/opening-files-from-command-line.html#bef13d46
  - `idea` コマンドに引数を設定するならこちら
- https://webbibouroku.com/Blog/Article/chmod-permission#outline__3
  - 権限周り、久しぶりに触るため安全か心配だったので色々確認させてもらった（初心者）