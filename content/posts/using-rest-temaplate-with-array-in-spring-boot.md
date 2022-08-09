---
title: "Spring Boot RestTemplate を使った REST API リクエスト時に List オブジェクトを返す方法"
date: 2022-08-09T06:35:08+09:00
tags: ["Spring Boot", "RestTemplate", "Java"]
---


# 困っていること(解決できること)
- RestTemplate を使う場合の Entity の記述方法がわからない
- RestTemplate で List オブジェクトを返す方法がわからない

## 補足
- 以下のサンプルコードは公式から引用し、個人的に参考となるポイントを記載しています。
- 本記事の主旨とは直接関係のない部分もありますがそこはご了承ください。

# サンプルコードと１つのオブジェクトを受け取る場合の実装方法

## Entity class ※[公式](https://spring.pleiades.io/guides/gs/consuming-rest/)から拝借

```java
import com.fasterxml.jackson.annotation.JsonIgnoreProperties;

@JsonIgnoreProperties(ignoreUnknown = true) // 1
public class Quote {

  private String type;
  private Value value;

  ... 省略
}

@JsonIgnoreProperties(ignoreUnknown = true) // 1
public class Value {

  private Long id; 
  private String quote;

  ... 省略
}
```

### ポイント
1. `JsonIgnoreProperties` Json 処理ライブラリ Jackson のアノテーションの一つ。これによって Json 変換処理をしないフィールドを複数まとめて設定できる。以下のように設定する。
```java
@JsonIgnoreProperties({"id", "age"})
```

## Sample application class ※[公式](https://spring.pleiades.io/guides/gs/consuming-rest/)から拝借
```java
@SpringBootApplication
public class ConsumingRestApplication {

	private static final Logger log = LoggerFactory.getLogger(ConsumingRestApplication.class);

	public static void main(String[] args) {
		SpringApplication.run(ConsumingRestApplication.class, args); // 2
	}

	@Bean
	public RestTemplate restTemplate(RestTemplateBuilder builder) {
		return builder.build();
	}

	@Bean
	public CommandLineRunner run(RestTemplate restTemplate) throws Exception { // 2
		return args -> {
			Quote quote = restTemplate.getForObject( // 3
					"https://quoters.apps.pcfone.io/api/random", Quote.class); 
			log.info(quote.toString());
		};
	}
}
```

### ポイント
2. `CommandLineRunner` はアプリケーション起動時に処理を走らせることができる `run` メソッドを持つインターフェース。このインターフェースを実装したクラスはコマンドラインアプリケーションとも呼ばれる。
    - 呼ばれるタイミングは DI するための Bean が登録された後、となる。
    - `ApplicationRunner` という類似のインターフェースがある。どちらも `run` メソッドを保持するが、引数に違いがある。詳しくは [こちら](https://stackoverflow.com/a/59329029) （一次情報ではない）

3. `getForObject()` を使用して、１つのオブジェクトを受け取ることが可能。
   1. 第一引数である URL に対し GET リクエストを送る。
   2. 第二引数に定義したクラスによって Json レスポンスをクラスにマッピングできる。
   - この際、内部で Jackson ライブラリを使用している。
   - もしパスパラメータを設定したい場合は第三引数に定義可能
   - これでは List 型で返ってこないため、List オブジェクトを返す方法を次の項目で説明する

# 複数配列 Json レスポンスを List 型のオブジェクトとして受け取る方法

## `run(RestTemplate restTemplate)` 内を `List.of(getForObject())` に修正する
```java
List<Quote> response = List.of(Objects.requireNonNull(restTemplate.getForObject(url, Quote[].class))); // 4
List<Quote> result = response.getBody();
```

### ポイント
4. `List.of()` で囲むことによって要素の追加・削除ができない配列を生成する。`Objects.requireNonNull()` で引数が null の場合に例外を出すようにする。
- `ParameterizedTypeReference` という方法もあります。

# References
- https://spring.pleiades.io/guides/gs/consuming-rest/
- https://qiita.com/opengl-8080/items/b613b9b3bc5d796c840c
