# 동시성 문제(1) - 동시성 문제 살펴보기

[후편 - MySQL에서 동시성 문제 해결하기](https://github.com/eastshine-high/til/blob/main/spring/spring-framework/blog/concurrency-Issue-2.md)

이번 페이지에서는 간단한 재고 감소 예제를 통해 동시성 이슈를 알아보겠습니다.

먼저, 상품 엔터티에 재고를 감소하는 로직( `decreaseStockQuantity` )을 작성합니다.

```java
@Table(name = "product")
@Entity
public class Product {

    @Id 
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private Integer stockQuantity;

    public void decreaseStockQuantity(int quantity) {
        if (this.stockQuantity - quantity < 0) {
            throw new InvalidArgumentException(ErrorCode.PRODUCT_STOCK_QUANTITY_ERROR);
        }

        this.stockQuantity -= quantity;
    }
}
```

서비스에서는 재고를 감소 시키고, 이를 저장합니다.

```java
@RequiredArgsConstructor
@Service
public class ProductService {
    private final ProductRepository productRepository;

    @Transactional
    public void decreaseStock(Long id, int quantity) {
        Product product = productRepository.findById(id).orElseThrow();
        product.decreaseStockQuantity(quantity);
    }
}
```

이제 동시 접속을 고려하여 테스트 코드를 작성해 보겠습니다.

```java
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

@Test
public void 동시에_100명이_주문() throws InterruptedException {
    int threadCount = 100;
    Product product = Product.builder()
            .stockQuantity(threadCount)
            .build();
    productRepository.save(product);

    ExecutorService executorService = Executors.newFixedThreadPool(32);
    CountDownLatch latch = new CountDownLatch(threadCount);

    for (int i = 0; i < threadCount; i++) {
        executorService.submit(() -> {
            try {
                systemProductService.decreaseStock(product.getId(), 1);
            } finally {
                latch.countDown();
            }
        });
    }

    latch.await();

    Product updatedProduct = productRepository.findById(product.getId()).orElseThrow();
    // 100 - (100 * 1) = 0
    assertThat(updatedProduct.getStockQuantity()).isEqualTo(0);
}
```

- `ExecutorService` 는 비동기로 실행할 수 있는 작업을 단순화하여 사용할 수 있게 도와주는 자바의 동시성 유틸 API입니다.
- `CountDownLatch` 는 다른 쓰레드에서 수행 중인 작업이 완료될 때까지 대기할 수 있도록 도와주는 클래스입니다.

하지만 이 테스트는 실패하였고, 재고는 예상과 달리 9개만 감소하였습니다.

```
org.opentest4j.AssertionFailedError: 
Expecting:
 <91>
to be equal to:
 <0>
but was not.
Expected :0
Actual   :91
```

이렇게 테스트를 실패하는 이유는 레이스 컨디션이 일어났기 때문입니다. 레이스 컨디션은 두 개 이상의 쓰레드가 공유 데이터에 동시에 접근할 수 있고, 동시에 변경하려고 할 때 발생하는 문제입니다.

먼저 우리는 다음과 같은 흐름을 기대했을 것입니다.

| Thread-1 | Product | Thread-2 |
| --- | --- | --- |
| select * from product where product_id = 1 | {product_id: 1, stock_quantity: 100} |  |
| update set stock_quantity = 99 from product where product_id = 1 | {product_id: 1, stock_quantity: 99} |  |
|  | {product_id: 1, stock_quantity: 99} | select * from product where product_id = 1 |
|  | {product_id: 1, stock_quantity: 98} | update set stock_quantity = 98 from product where product_id = 1 |

- `Thread-1` 이 `stock_quantity` 가 100인 데이터를 읽고 `1` 을 감소하여 `stock_quantity` 는 `99` 가 됩니다.
- 그 뒤에  `Thread-2` 가 `stock_quantity` 가 `99` 인 데이터를 읽고 `1` 을 감소하여 `stock_quantity` 는 `98` 가 됩니다.

하지만 실제로는 다음과 같이 진행됩니다.

| Thread-1 | Product | Thread-2 |
| --- | --- | --- |
| select * from product where product_id = 1 | {product_id: 1, stock_quantity: 100} |  |
|  | {product_id: 1, stock_quantity: 100} | select * from product where product_id = 1 |
| update set stock_quantity = 99 from product where product_id = 1 | {product_id: 1, stock_quantity: 99} |  |
|  | {product_id: 1, stock_quantity: 99} | update set stock_quantity = 99 from product where product_id = 1 |

- `Thread-1` 이 데이터를 읽고 갱신하기 전에 `Thread-2` 도 값을 읽어들입니다.
- 두 쓰레드 모두 같은 데이터( `100` )를 읽고 갱신하였기 때문에 갱신이 누락되어 `stock_quantity` 의 최종 값은 `99` 가 됩니다.

이러한 레이스 컨디션 문제를 해결하기 위해서는 하나의 쓰레드가 작업을 완료된 후에 다른 쓰레드가 데이터에 접근할 수 있도록 해야 합니다.

```java
@RequiredArgsConstructor
@Service
public class ProductService {
    private final ProductRepository productRepository;

    public synchronized void decreaseStock(Long id, int quantity) {
        Product product = productRepository.findById(id).orElseThrow();

        product.decreaseStockQuantity(quantity);

        productRepository.saveAndFlush(product);
    }
}
```

자바에서는 `synchronized` 를 선언하여 한 개의 쓰레드만 메소드에 접근하도록 할 수 있습니다. 이제 위의 테스트 코드는 정상적으로 통과합니다.

하지만 우리가 자주 사용하는 MySQL, Oracle과 같은 관계형 데이터베이스는 멀티 쓰레드로 동작합니다. 만약 웹 어플리케이션 서버를 하나 더 증설했다고 가정해 봅니다. 이 경우에도 마찬가지로 레이스 컨디션 문제가 발생합니다.

| Server-1 | Product | Server-2 |
| --- | --- | --- |
| select * from product where product_id = 1 | {product_id: 1, stock_quantity: 100} |  |
|  | {product_id: 1, stock_quantity: 100} | select * from product where product_id = 1 |
| update set stock_quantity = 99 from product where product_id = 1 | {product_id: 1, stock_quantity: 99} |  |
|  | {product_id: 1, stock_quantity: 99} | update set stock_quantity = 99 from product where product_id = 1 |

따라서 데이터베이스에서 지원하는 방법으로 레이스 컨디션 문제를 해결해야 합니다. 다음 페이지에서는 [MySQL을 이용해 데이터 정합성을 맞추는 방법](https://github.com/eastshine-high/til/blob/main/spring/spring-framework/blog/concurrency-Issue-2.md)에 대해서 알아보겠습니다.

---

**참조**

<최상용, 재고시스템으로 알아보는 동시성이슈 해결방법, 인프런>
