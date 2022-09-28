---
title: "Lombok のアノテーション"
date: 2022-09-27T09:17:42+09:00
tags: ["Java", "Lombok"]
draft: true
---

# はじめに
普段使っているアノテーションを整理するためにメモに残しとこうと思います。基本的に[こちら](https://blog.y-yuki.net/entry/2016/10/13/003000)を引用しています。

# コンストラクタ生成タイプのアノテーション
## @NoArgsConstructor
デフォルトコンストラクタを生成するもの（メンバー変数なし）
```java
import lombok.NoArgsConstructor;

@NoArgsConstructor
public class Person1 {
    private long id;
    private String name;
    private int age;

    // 実際には以下が追加される
    public Person1() {
    }
}
```

## @AllArgsConstructor
すべてのメンバー変数の値をセットできるコンストラクタが自動で生成される

```java
import lombok.AllArgsConstructor;

@AllArgsConstructor
public class Person2 {
    private long id;
    private String name;
    private int age;

    // 実際には以下が追加される
    public Person2(final long id, final String name, final int age) {
        this.id = id;
        this.name = name;
        this.age = age;
    }
}
```

## @RequiredArgsConstructor
すべての `final` メンバー変数の値をセットできるコンストラクタが自動で生成される

```java
import lombok.RequiredArgsConstructor;

@RequiredArgsConstructor
public class Person4 {

    private final long id;
    private String name;
    private int age;

    // 実際には以下が追加される
    public Person4(final long id) {
        this.id = id;
    }
}
```

## 組み合わせパターン
上記で紹介したアノテーションは組み合わせることが可能

### @RequiredArgsConstructor + @AllArgsConstructor

```java
import lombok.AllArgsConstructor;
import lombok.RequiredArgsConstructor;

@RequiredArgsConstructor
@AllArgsConstructor
public class Person5 {

    private final long id;
    private String name;
    private int age;

    // 実際には以下が追加される
    public Person5(final long id) {
        this.id = id;
    }
    public Person5(final long id, final String name, final int age) {
        this.id = id;
        this.name = name;
        this.age = age;
    }
}
```

### @NoArgsConstructor + @AllArgsConstructor
```java
import lombok.AllArgsConstructor;
import lombok.NoArgsConstructor;

@NoArgsConstructor
@AllArgsConstructor
public class Person3 {

    private long id;
    private String name;
    private int age;

    // 実際には以下が追加される
    public Person3() {}
    public Person3(final long id, final String name, final int age) {
        this.id = id;
        this.name = name;
        this.age = age;
    }
}
```

## TODO

# References
- https://blog.y-yuki.net/entry/2016/10/13/003000