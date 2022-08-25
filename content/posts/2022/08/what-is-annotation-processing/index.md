---
title: "Annotation Processing とは"
date: 2022-08-26T03:50:04+09:00
tags: ["Java", "Spring Boot", "Annocation Processing"]
---

Annocation Processing とは Java 1.8 から導入されている Pluggable Annotation Processing API を指していて、コンパイル時にアノテーションを処理する仕組みのこと。
コンパイル後に処理されたデータは「.apt_generated」に格納される。

カスタムアノテーションを構成することもできるようで IntelliJ IDEA で行う場合は[こちら](https://pleiades.io/help/idea/annotation-processors-support.html)を参照するとよさそう。



# References
- https://www.baeldung.com/java-annotation-processing-builder
  - こんなチュートリアルもあった