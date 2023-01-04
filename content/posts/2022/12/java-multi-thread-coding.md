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
今回は使い方2である Runnable インターフェースを用いて実装した。コード量が多く、Service クラスや Domain クラスは省いているためこのまま実行はできないことに留意。

```java
public class ProductServiceUsingThread {
    private ProductInfoService productInfoService;
    private ReviewService reviewService;

    public ProductServiceUsingThread(ProductInfoService productInfoService, ReviewService reviewService) {
        this.productInfoService = productInfoService;
        this.reviewService = reviewService;
    }

    public Product retrieveProductDetails(String productId) throws InterruptedException {
        stopWatch.start();
        ProductServiceUsingThread.ProductInfoRunnable productInfoRunnable = new ProductServiceUsingThread.ProductInfoRunnable(productId);
        Thread productInfoThread = new Thread(productInfoRunnable);

        ProductServiceUsingThread.ReviewRunnable reviewRunnable = new ProductServiceUsingThread.ReviewRunnable(productId);
        Thread reviewThread = new Thread(reviewRunnable);

        // start functionalities as background tasks
        productInfoThread.start();
        reviewThread.start();

        // join will wait tasks until productInfoThread will complete.
        productInfoThread.join();
        // reviewThread as well
        reviewThread.join();

        // once came here, it means the tasks already completed
        ProductInfo productInfo = productInfoRunnable.getProductInfo();
        Review review = reviewRunnable.getReview();

        stopWatch.stop();
        log("Total Time Taken : "+ stopWatch.getTime());
        return new Product(productId, productInfo, review);
    }

    public static void main(String[] args) throws InterruptedException {
        ProductInfoService productInfoService = new ProductInfoService();
        ReviewService reviewService = new ReviewService();
        ProductServiceUsingThread productService = new ProductServiceUsingThread(productInfoService, reviewService);
        String productId = "ABC123";
        Product product = productService.retrieveProductDetails(productId);
        log("Product is " + product);

    }

    // Thread を使ってサービスを呼び出す用のクラス
    private class ProductInfoRunnable implements Runnable {
        private ProductInfo productInfo;
        private String productId;
        public ProductInfoRunnable(String productId) {
            this.productId = productId;
        }

        public ProductInfo getProductInfo() {
            return productInfo;
        }

        @Override
        public void run() {
            productInfo = productInfoService.retrieveProductInfo(productId);
        }
    }

    // Thread を使ってサービスを呼び出す用のクラス
    private class ReviewRunnable implements Runnable {
        private String productId;
        private Review review;
        public ReviewRunnable(String productId) {
            this.productId = productId;
        }

        public Review getReview() {
            return review;
        }

        @Override
        public void run() {
            review = reviewService.retrieveReviews(productId);
        }
    }
}
```

- 実行結果は以下となる。

```log
> Task :ProductServiceUsingThread.main()
[main] - Total Time Taken : 1012
[main] - Product is Product(productId=ABC123, productInfo=ProductInfo(productId=ABC123, productOptions=[ProductOption(productionOptionId=1, size=64GB, color=Black, price=699.99, inventory=null), ProductOption(productionOptionId=2, size=128GB, color=Black, price=749.99, inventory=null)]), review=Review(noOfReviews=200, overallRating=4.5))

BUILD SUCCESSFUL in 1s
2 actionable tasks: 2 executed
```

- コード例では省いているが、ReviewService クラス、ProductInfoService クラスで `delay(1000);` 処理を実行している。
- それによって各サービスで最低1秒は遅延が発生するのだが、実行時間が 1012 しか経っていないことから並列で処理できているとわかる。

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