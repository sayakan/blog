---
title: "azure-sdk-bom と spring-cloud-dependencies のバージョンを合わせる"
date: 2023-02-11T03:05:25+09:00
tags: ["Azure", "SDK", "Maven", "Java"]
---

# 初めに
今開発している Maven プロジェクトでは、基本は `spring-cloud-dependencies` を使って spring-cloud-azure の依存関係を管理しているが、ピュアな azure-sdk を使わなければいけない場面があったので共存させる場合のバージョンを調査した。

# 結論
spring-cloud-azure と ピュア azure-sdk の共存には以下のサイトを見てバージョンを合わせましょう
https://github.com/Azure/azure-sdk-for-java/wiki/Spring-Versions-Mapping

# 経緯
```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-storage-blob</artifactId>
    <version>${azure-storage-blob.version}</version>
</dependency>
```
当初 pom には上記のように依存関係を追加していたが、azure-core にある jackson のバージョンと前述の `spring-cloud-dependencies` が競合してしまっていた。
検索すると[こちら](https://learn.microsoft.com/ja-jp/azure/developer/java/sdk/troubleshooting-dependency-version-conflict)が最初に引っかかるがこれだけでは知りたいことがわからず。

結論にも書いた[こちら](https://github.com/Azure/azure-sdk-for-java/wiki/Spring-Versions-Mapping)のサイトで最新の Spring Boot3 には 1.2.8 を使えばいいことがわかった。

最終的に以下のようになった。（一部抜粋）
```xml
<!-- BOM -->

<properties>
    <!-- SpringのAzure関連ライブラリはここでバージョンを指定する -->
    <spring-cloud-azure.version>5.0.0</spring-cloud-azure.version>

    <!-- Spring以外ののAzure関連ライブラリはここでバージョンを指定する -->
    <azure-sdk.version>1.2.8</azure-sdk.version>
</properties>

<dependencyManagement>
    <dependencies>
    ...
        <dependency>
            <groupId>com.azure.spring</groupId>
            <artifactId>spring-cloud-azure-dependencies</artifactId>
            <version>${spring-cloud-azure.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
        <dependency>
            <groupId>com.azure</groupId>
            <artifactId>azure-sdk-bom</artifactId>
            <version>${azure-sdk.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    ...
    </dependencies>
</dependencyManagement>
```

# 終わりに
バージョンを Spring Boot3 にあげたから色々気をつけねば。。

# References
- https://learn.microsoft.com/ja-jp/azure/developer/java/sdk/troubleshooting-dependency-version-conflict