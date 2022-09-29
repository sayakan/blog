---
title: "Os.detected.classifier を M1 Mac で使用する"
date: 2022-09-29T11:32:16+09:00
tags: ["Spring Boot", "Java", "Protocol Buffers", "Maven"]
---

# はじめに
Windows で開発された Spring Boot アプリケーションを起動しようとしても、Maven の pom ファイルでエラーが発生してしまい動かなかったので調査した。

# 環境
|  Name  |  Version  |
| ---- | ---- |
|  macOS Monterey(M1 Macbook Pro) |  12.1 |
|  JetBrains Toolbox App  |  1.25  |
|  IntelliJ IDEA (Ultimate Edition) |  2022.2 |

# 問題点
以下を M1 Mac で実行しようとすると os.detected.classifier が読み込めない

```xml
<plugin>
    <groupId>org.xolstice.maven.plugins</groupId>
    <artifactId>protobuf-maven-plugin</artifactId>
    <version>0.6.1</version>
    <extensions>true</extensions>
    <executions>
        <execution>
            <goals>
                <goal>compile</goal>
                <goal>test-compile</goal>
            </goals>
        </execution>
    </executions>
    <configuration>
        <protocArtifact>com.google.protobuf:protoc:3.7.0:exe:${os.detected.classifier}</protocArtifact>
    </configuration>
</plugin>
```

# 解決策
[github issue](https://github.com/grpc/grpc-java/issues/7690) にも上がっていて、いくつか解決策がある。
私は `~/.m2/settings.xml` に以下を追加する形で対応した。

```xml
<settings>
  ...
  <activeProfiles>
    <activeProfile>
      apple-silicon
    </activeProfile>
    ...
  </activeProfiles>
  <profiles>
    <profile>
      <id>apple-silicon</id>
      <properties>
        <os.detected.classifier>osx-x86_64</os.detected.classifier>
      </properties>
    </profile>
    ...
  </profiles>
  ...
</settings>
```

# 終わりに
対応後は Maven の Reload をすることを忘れずに。
