---
title: "Java CompletableFuture"
date: 2023-01-08T15:38:10+09:00
tags: ["Java", "Programming", "CompletableFuture", "非同期"]
---

# CompletableFuture とは
- Java8 で導入された Java の並行処理 API
- CompletableFuture は関数型プログラミングスタイルを使用した非同期処理とリアクティブ API が実行できる
- Future / CompletionStage を継承した便利クラス

## Reactive Programming と CompletableFuture

### Reactive Programming とは
- データをストリームとして認識
  - データを受け取るたびにプログラムが反応して処理を行うこと
  - 必要なデータを自ら取得して処理をするスタイルではない
- データの生産側はデータの消費側がどういう処理をしているのか意識しない
  - データの消費側の処理を待つ必要がなくなる→非同期
  - データを通知した後はすぐに別の次の処理を行うことができる→ノンブロッキング
  - データの消費者の負荷状況に関わらず一方的にデータを通知し続ける状況への対応手段の提供→バックプレッシャー

## リアクティブシステムの性質
- Reactive API のようなリアクティブシステムには以下４つの性質がある([Reactive manifesto](https://www.reactivemanifesto.org/ja))
  1. Responsive（即応性）
     - 基本的に非同期
     - リクエストはすぐ呼び出され、データが利用可能な時に呼び出し側へレスポンスが返される
  2. Resilient（耐障害性）
     - システムは例外やエラーなどの障害に直面しても即応性を保ち続ける
     - 耐障害性は、レプリケーション・封じ込め・隔離・移譲によって実現される
  3. Elastic（弾力性）
     - システムはワークロードが変動しても即応性を保ち続ける
     - リアクティブシステムは入力の提供に割り当てるリソースを増加あるいは減少させることで入力量の変化に反応する
       - これは、システムの中に競合する場所や中心的なボトルネックが存在しないように設計し、シャーディングしたりレプリケーションしたコンポーネント間に入力を分散させることを意味する
  4. Message Driven（メッセージ駆動）
     - リアクティブシステムは非同期なメッセージパッシングによってコンポーネント間の境界を確立する
       - これによって疎結合性・隔離性・位置透過性を保証するとともに、エラーをメッセージとして移譲する手段を確保する
     - システム内にメッセージキューを作成して監視し、必要ならバックプレッシャーを適用することでフロー制御が可能になる

# CompletableFuture API
- `CompletableFuture` クラスのメソッドは三つのカテゴリに分けられる
  1. Factory メソッド
     - 非同期計算を開始する 
  2. Completion Stage メソッド
     - 非同期計算を実現するために使用される
  3. 例外メソッド
     - 例外を処理する

## `supplyAsync()` / `thenAccept()`
### `supplyAsync()`
- Factory メソッド
- 非同期計算を開始するために使用される。バックグラウンドのタスクをトリガーし、すぐに返す
- 二つのリアクティブシステムの性質を持つ
  - Responsive. 基本的に非同期で呼び出されるとすぐに返される
  - Message Driven. `supplyAsync()` からのデータが返された時、一部を除いて `thenAccept()` にメッセージの形でデータが流される
### `thenAccept()`
- CompletionStage メソッド
- 非同期計算をつなげて処理する
- 前の非同期計算の結果を取得し、それに対して何らかのアクションを行う

## Example Code

```java

public class CompletableFutureHelloWorld {
    public static void main(String[] args) {
        HelloWorldService hws = new HelloWorldService();

        // ①
        CompletableFuture.supplyAsync(hws::helloWorld)
                // ②
                .thenApply(String::toUpperCase)
                // ③
                .thenAccept((result)-> {
                    log("result is: " + result);
                })
                .join();
        // this log should be showed at first if we don't put join().
        log("done!");
    }
}
```

### ①非同期処理の呼び出し
- `supplyAsync()` は非同期計算を呼び出すファクトリーメソッド
- このケースではメインスレッドから解放され、共通の fork-join プールで実行される
- 後の処理で `join()` を使用しているため、このコードではまだメインスレッドをブロックしている状態だが、使用しなければ別スレッドで処理される
- `helloWorld()` は1秒の遅延処理がある

### ②中間処理
- `thenApply()` は、データをインプットとして受け取り、処理した結果をチェーンのように次のメソッドへ引き渡す

### ③終端処理
- `thenAccept()` は、データを処理する。処理した結果を次のメソッドに送ることはできない
- `join()` は全ての非同期処理が終了するまでメインスレッドをブロックして待つ
- 後の `join()` がなければ、実行しても別スレッドで処理されれば遅延を待たずにメインスレッドが処理を終了するため実行ログに表示されない

## Example Test Code

```java
class CompletableFutureHelloWorldTest {

    private final HelloWorldService hws = new HelloWorldService();
    private CompletableFutureHelloWorld cfhw = new CompletableFutureHelloWorld(hws);

    @Test
    void helloWorld() {
        // given

        // when
        CompletableFuture<String> cf = cfhw.helloWorld();

        // then
        cf
        .thenAccept(s-> {
            assertEquals("HELLO WORLD", s);
            })
        .join();

    }
}
```

# References
- https://speakerdeck.com/oracle4engineer/
- https://www.reactivemanifesto.org/ja