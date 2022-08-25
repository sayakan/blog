---
title: "Blob Storage の変更フィードと Cosmos DB の変更フィードの違い"
date: 2022-08-23T22:15:49+09:00
tags: ["CosmosDB", "Azure Blob Storage", "Azure"]
draft: true
---

イベントソーシングを行うために JSON データを保存する場合において、選択肢が①Blob Storage ②Cosmos DB があったため、どちらを使うか考えるため変更フィードについてまとめる。

# Blob Storage 変更フィード

## ユースケース
- 変更フィードの目的は、ストレージ アカウント内の BLOB と BLOB メタデータに対して行われるすべての変更のトランザクション ログを提供すること
- 変更フィードでは、これらの変更の順序指定済み、保証済み、永続、不変、読み取り専用ログが提供される
-  クライアント アプリケーションでは、ストリーミングまたはバッチ モードで、いつでもこれらのログを読み取ることができる
-  使用すると、低コストで、BLOB ストレージ アカウントで発生する変更イベントを処理する効率的でスケーラブルなソリューションを構築できる

## 変更フィードの仕組み
- 変更フィードレコードは、標準の BLOB 価格コストで、ストレージ アカウントの特別なコンテナーに BLOB として保存される
- これらのファイルの保有期間は、要件に基づいて制御できる
-  変更イベントは、Apache Avro 形式仕様のレコードとして変更フィードに追加される

# Cosmos DB 変更フィード
TODO

# References
- https://docs.microsoft.com/ja-jp/azure/storage/blobs/storage-blob-change-feed?tabs=azure-portal
  - Blob Storage 変更フィード
- https://docs.microsoft.com/ja-jp/azure/storage/blobs/storage-blob-change-feed?tabs=azure-portal#conditions
- https://docs.microsoft.com/ja-jp/azure/storage/blobs/storage-blob-change-feed?tabs=azure-portal#should-i-use-the-change-feed-or-storage-events
  - 変更フィードとストレージ イベントのどちらを使用するべきか
- https://particular.net/blog/putting-your-events-on-a-diet
  - イベントデータを減らすには
- https://eventuate.io/whyeventsourcing.html
  - イベントソーシング