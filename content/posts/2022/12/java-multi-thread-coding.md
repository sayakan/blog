---
title: "Java Multi Thread Coding"
date: 2022-12-30T15:57:40+09:00
tags: ["Java", "Programming"]
---

# はじめに
Java のマルチスレッドや非同期処理、並列処理の仕様についてなんとなくの知識しか持っていなかったため調査した。簡単にまとめたものをメモとして残す。

# Thread
> スレッドとは、プログラム内での実行スレッドのことです。Java仮想マシンでは、アプリケーションは並列に実行される複数のスレッドを使用することができます。
> 引用元：https://docs.oracle.com/javase/jp/8/docs/api/java/lang/Thread.html

- スレッドは、一つのまとまった処理を表す。
- 実行時に一つのスレッドのみを扱うことをシングルスレッドと呼ぶ。
- スレッドが複数用意され、各スレッドが同時に並列処理が行われることをマルチスレッドと呼ぶ。

## Thread の使い方
1. Thread クラスを継承する
2. Runnable インターフェースを実装する

## Example: Runnable インターフェースを実装してみる
今回は使い方2である Runnable インターフェースを用いて実装した。

## ExecutorService

TODO

## Fork/Join Framework
- Streams API が導入される以前の Java 7 では再帰的な書き方として使われていた
- Streams API と比べるとコードが複雑になり、可読性が落ちる

## Streams API
- Java 8 の一部として導入された API
- オブジェクトのコレクション（List, Map, Set）を処理するために使われる
- `stream()` メソッドを用いて作成される。

### Code Example
- 以下は Streams API を用いて数値のリストを作成し処理していくコードとなる。

```java
List<Integer> integerList = Arrays.asList(1, 2, 3, 4, 5);
integerList.stream() // ①開始操作
        .filter(i -> i % 2 == 0) // ②中間操作
        .forEach(i -> System.out.println(i)); // ③終端操作
```

- 上記のようにコードはチェーンとして繋げることができ、一つの塊はパイプラインになっている。処理は3段階に分けられる。

#### ①開始操作
- まずリストオブジェクトに対して Streams API を呼び出している

#### ②中間操作
- パイプラインの中間に当たる処理を記述する（`filter`, `map`, etc）
- Streams API を呼び出して処理してから終端操作が行われるまでの処理は全て中間操作となる
- コードでは、各値を2で割り、0になるものを抽出する
#### ③終端操作
- Streams API 全体に対して終端処理が行われる
- コードでは、`forEach()` メソッドを用いて抽出した値を順にログに出している

## ParallelStreams
- データの並列処理を行うために Java8 で導入された機能の一つ
- `stream()` も `parallelStream()` も一部のメソッドを除いて同じようにシーケンシャルに記述できる
- Java8 以前では並列処理を実行するには多くのコードを書く必要があったが、構造が改善されたため書き方は Java7 以前とは大きく異なっている

### Code Example
- 開始操作を `stream()` -> `parallelStream()` に変更するだけで使用できる

```java
List<Integer> integerList = Arrays.asList(1, 2, 3, 4, 5);
integerList.parallelStream() // ①開始操作
        .filter(i -> i % 2 == 0) // ②中間操作
        .forEach(i -> System.out.println(i)); // ③終端操作
```


# References