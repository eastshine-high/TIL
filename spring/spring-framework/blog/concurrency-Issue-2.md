# 동시성 문제(2) - MySQL에서 동시성 문제 해결하기

[전편 - 동시성 문제 살펴보기](https://github.com/eastshine-high/til/blob/main/spring/spring-framework/blog/concurrency-Issue-1.md)

MySQL에서는 다음 3가지 방법으로 데이터 정합성을 맞출 수 있습니다.

- Pessimistic Lock
- Optimistic Lock
- Named Lock

## Pessimistic Lock

실제로 데이터에 Lock을 걸어서 정합성을 맞추는 방법입니다. exclusive lock을 걸게되면 다른 트랜잭션에서는 lock이 해제되기전에 데이터를 읽을 수 없게됩니다. 별도의 Lock을 잡기 때문에 성능 감소가 일어날 수 있습니다. 또한 데드락이 걸릴 수 있기 때문에 주의하여 사용하여야 합니다. 충돌이 빈번하게 일어난다면 Optimistic Lock 보다 성능이 좋을 수 있습니다.

Pessimistic Lock은 Spring Data JPA의 기능을 활용하면 간단히 구현할 수 있습니다.

엔터티 정의

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

리포지토리 정의
```java
public interface ProductRepository extends JpaRepository<Product, Long> {

    @Lock(value = LockModeType.PESSIMISTIC_WRITE)
    @Query("select p from Product p where p.id = :id")
    Product findByIdWithPessimisticLock(@Param("id") Long id);
}
```

Service 코드

```java
@RequiredArgsConstructor
@Service
public class SystemProductService {
    private final ProductRepository productRepository;

    @Transactional
    public void decreaseStock(Long id, int quantity) {
        Product product = productRepository.findByIdWithPessimisticLock(id);
        product.decreaseStockQuantity(quantity);
        productRepository.saveAndFlush(product);
    }
}
```

실행 로그

```
select product0_.stock_quantity as stock_q11_1_ ... 중략 
from product product0_ where product0_.product_id=1 for update;
```

실행 로그를 확인해 보면 끝에 `for update` 이 실행된 것을 확인할 수 있습니다. 이 부분을 통해 lock을 걸고 데이터를 가지고 오는 것을 확인할 수 있습니다.

## Optimistic Lock

실제로 Lock을 이용하지 않고 버전을 이용하여 정합성을 맞추는 방법입니다. 먼저 데이터를 읽은 후에 update 를 수행할 때 현재 내가 읽은 버전이 맞는지 확인하여 업데이트 합니다. 내가 읽은 버전에서 수정사항이 생겼을 경우에는 application에서 다시 읽은 후에 작업을 수행해야 합니다.

| Server 1 | MySQL | Server 2 |
| --- | --- | --- |
| select * from product where product_id = 1 | {product_id: 1, stock_quantity: 100, version: 1} | read(product_id: 1, stock_quantity: 100, version: 1) |
| update stock_quantity = 98, version = version +1 from product where product_id = 1 and version = 1 | {product_id: 1, stock_quantity: 99, version: 2} |  |
|  | {product_id: 1, stock_quantity: 99, version: 2} | update stock_quantity = 98, version = version +1 from product where product_id = 1 and version = 1 `FAIL` |

1. `Server1` 과 `Server2` 모두 `version = 1` 인 데이터를 읽습니다.
2. `Server1` 이 `update` 를 수행할 때, `version` 의 값을 `1` 증가시킵니다.
3. `Server2` 는 `version = 1` 인 조건의 `update` 를 수행하므로, `Server2` 의 `update` 는 실패합니다.

**Spring Data JPA를 이용한 구현**

Entity에 `version` 컬럼을 추가합니다.

```java
@Table(name = "product")
@Entity
public class Product {

		@Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "product_id")
    private Long id;
		private Integer stockQuantity;

		@Version
    private Long version;

		public void decreaseStockQuantity(int quantity) {
        if (this.stockQuantity - quantity < 0) {
            throw new InvalidArgumentException(ErrorCode.PRODUCT_STOCK_QUANTITY_ERROR);
        }

        this.stockQuantity -= quantity;
    }
}

```

쿼리 메소드에 `OPTIMISTIC` Lock을 설정합니다.

```java
public interface ProductRepository extends JpaRepository<Product, Long>, ProductRepositoryCustom {

    @Lock(value = LockModeType.OPTIMISTIC)
    @Query("select p from Product p where p.id = :id")
    Product findByIdWithOptimisticLock(@Param("id") Long id);
}
```

서비스 코드를 작성합니다.

```java
@RequiredArgsConstructor
@Service
public class SystemProductService {
    private final ProductRepository productRepository;

    @Transactional
    public void decreaseStock(Long id, int quantity) {
        Product product = productRepository.findByIdWithOptimisticLock(id);
        product.decreaseStockQuantity(quantity);
        productRepository.saveAndFlush(product);
    }
}
```

컨트롤러에서는 업데이트의 실패를 대비해 재시도 로직을 작성합니다.

```java
@PostMapping("/{id}/decrease-stock")
public void decreaseStock(@PathVariable Long id, int quantity) throws InterruptedException {
    while (true) {
        try {
            systemProductService.decreaseStock(id, quantity);
            break;
        } catch (Exception e) {
            Thread.sleep(50);
        }
    }
}
```

- `Thread.sleep(50)` : 실패할 경우 50 millis 이후 재시도 합니다.
- 성공할 경우 `break` 를 통해 로직 수행을 빠져나옵니다.

Optimistic Lock은 별도의 Lock을 잡지 않으므로 Pessimistic Lock보다 성능상의 이점이 있습니다. 충돌이 빈번하게 일어난다면 Pessimistic Lock보다 성능이 좋지 않을 수 있습니다. Update가 실패했을 경우, 개발자가 직접 재시도 로직을 작성해야 합니다.

## Named Lock

- 이름을 가진 Metadata locking 입니다. 이름을 가진 Lock을 획득한 후 해제할 때까지 다른 세션은 이 Lock을 획득할 수 없도록 합니다.
- Pessimistic Lock은 타임아웃을 구현하기 힘들지만 Named Lock은 손쉽게 이를 구할 수 있습니다.
- 데이터 삽입 시에 정합성을 맞출 때도 Named Lock을 사용할 수 있습니다.
- 주의할 점으로는 Transaction이 종료될 때 Lock이 자동으로 해제되지 않습니다. 별도의 명령어로 해제를 수행해 주거나 선점시간이 끝나야 해제됩니다. 따라서 트랜잭션 종료 시에 Lock해제와 세션 관리를 잘 해주어야 하기 때문에 주의해서 사용해야 합니다.
- 주로 분산 락을 구현할 때 사용합니다. 이를 구현하는 방법은 [우아한형제들 기술블로그](https://techblog.woowahan.com/2631/)에 잘 정리되어 있으니 참조바랍니다.

---

**참조**

<최상용, 재고시스템으로 알아보는 동시성이슈 해결방법, 인프런>
