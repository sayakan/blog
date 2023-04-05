---
title: "Wire Mock で NoClassDefFoundError が発生する"
date: 2023-04-05T18:08:52+09:00
tags: ["Java", "WireMock", "Spring Boot"]
---


## エラー
Spring Boot3 で WireMock を導入しようとしたら以下のエラーが出た。

```
Exception in thread "main" java.lang.NoClassDefFoundError: javax/servlet/DispatcherType
	at java.base/java.lang.Class.forName0(Native Method)
	at java.base/java.lang.Class.forName(Class.java:375)
	at com.github.tomakehurst.wiremock.jetty9.JettyHttpServerFactory.getServerConstructor(JettyHttpServerFactory.java:37)
	at com.github.tomakehurst.wiremock.jetty9.JettyHttpServerFactory.<clinit>(JettyHttpServerFactory.java:30)
	at com.github.tomakehurst.wiremock.core.WireMockConfiguration.<init>(WireMockConfiguration.java:94)
	at com.github.tomakehurst.wiremock.core.WireMockConfiguration.wireMockConfig(WireMockConfiguration.java:135)
	at com.github.tomakehurst.wiremock.core.WireMockConfiguration.options(WireMockConfiguration.java:139)
	at com.github.tomakehurst.wiremock.junit5.WireMockExtension.<clinit>(WireMockExtension.java:40)
```

## 対処法

以下を
```xml
<dependency>
    <groupId>com.github.tomakehurst</groupId>
    <artifactId>wiremock-jre8</artifactId>
    <version>2.35.0</version>
    <scope>test</scope>
</dependency>
```

に変えた。
```xml
<dependency>
    <groupId>com.github.tomakehurst</groupId>
    <artifactId>wiremock-jre8-standalone</artifactId>
    <version>2.35.0</version>
    <scope>test</scope>
</dependency>
```


# References