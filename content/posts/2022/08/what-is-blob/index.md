---
title: "What Is Blob"
date: 2022-08-15T01:59:59+09:00
tags: ["Azure Blob Storage", "Azure"]
---

# 概要
## [Azure Blob Storage](https://docs.microsoft.com/ja-jp/azure/storage/blobs/storage-blobs-introduction) とは
Azure のストレージサービス。安価で大量の非構造化データが保存できる。
> 非構造化データとは、特定のデータ モデルや定義に従っていないデータであり、テキスト データやバイナリ データなど

ユーザーまたはクライアント アプリケーションは、世界のどこからでも、HTTP/HTTPS 経由で Blob Storage 内のオブジェクトにアクセスできる。

## リソースの種類
Blob リソースには3種類存在する。ストレージアカウント→コンテナ→Blob の順で構築が必要となる。

![Azure 公式より引用](https://docs.microsoft.com/ja-jp/azure/storage/blobs/media/storage-blobs-introduction/blob1.png)

### 1. ストレージアカウント
- Azure 内にユニークなネームスペースを準備する
- 格納されたオブジェクトにはそれぞれネームスペースを用いたアドレスが発行されることとなる
- 以下はその一例（アカウント名：mystorageaccount）**ハイフンなどが使えないことに留意**
```
http://mystorageaccount.blob.core.windows.net
```

### 2. コンテナ
- ファイル システムのディレクトリと同じように、コンテナーを使用して BLOB のセットを整理する
- コンテナ数や Blob 数に制限はない

### 3. BLOB
コンテナ内に格納されるオブジェクトのこと。3 種類の BLOB がある。
#### ブロック BLOB
- テキストとバイナリ データが格納される。
- 最大約 190.7 TiB のデータを格納できる
#### 追加 BLOB
> ブロック BLOB と同様にブロックで構成されますが、追加操作用に最適化されています。 追加 BLOB は、仮想マシンのデータのログ記録などのシナリオに最適
#### ページ BLOB 
- ランダムなアクセスを必要とするユースケースに最適
- 詳細は[こちら](https://docs.microsoft.com/ja-jp/azure/storage/blobs/storage-blob-pageblob-overview?tabs=dotnet)を参照のこと

# 作成してみる
## ストレージアカウントの作成
[ストレージ アカウントを作成する](https://docs.microsoft.com/ja-jp/azure/storage/common/storage-account-create?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal) を参考に作成を行う

## コンテナの作成
[クイック スタート:Azure portal を使用して BLOB をアップロード、ダウンロード、および一覧表示する](https://docs.microsoft.com/ja-jp/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container) のセクション「コンテナーを作成する」を参考に作成を行う

## ブロック BLOB をアップする
コンテナ作成時と同じ資料を参照し、[ブロック BLOB をアップロードする](https://docs.microsoft.com/ja-jp/azure/storage/blobs/storage-quickstart-blobs-portal#upload-a-block-blob) セクションを参考にアップロードを行う


# 終わりに
以上で終了です。アカウント・コンテナが不要な場合は削除することをお勧めします。


# References
- https://docs.microsoft.com/ja-jp/azure/storage/blobs/storage-blobs-introduction
  - 基本的にこの資料を自分用にメモしただけ